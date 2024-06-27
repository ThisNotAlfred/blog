---
title: "What if `printf`, But new?"
author: Mohammad Ali Talebi
categories:
  - Programming
tags:
 - C++
last_modified_at: 2024-06-27
---

Printfs is one of those functions that we might use in C programming everyday. What if we could take advantage of new C++ features and write a new and polished `printf`?

## What do we need?
First we have to get the string from the user then parse at and put the arguments inside of it. Also you might need at least two brain cells (like me) to process the blog post. That’s it. Easy huh? By the way, you can find the full source code on my github.

## The scaffolding
Our scaffolding is something like this.

```c++
template <typename Arg>
    requires std::integral<Arg>
inline constexpr auto
parse_number(Arg arg)
{
}

template <typename Arg, typename... Args>
auto
r_printf(std::string& buffer, const std::string& format, const Arg& arg, const Args&... args)
{
}

namespace modern
{
template <typename... Args>
constexpr auto
printf(const std::string& format, const Args&... args) -> void
{
}
```

I don’t think that I needed to use namespaces here, but I did anyway. but it's always good to use namespaces, since it aviods name clashing with other libraries.

`printf` is our main function. It’s the one that we’re going to use. `r_printf` is used for taking variadic arguments. We can see that later.

First let’s check something. Let’s assume out functions calls are something like below:

```c++
    modern::printf(“{}, {}!”, “hello”, “world”);
```

We should basically parse for curly brackets and replace them in the string for the arguments.

We first make sure that we have the right amount of arguments, count the curly pairs for the arguments. The code is simple and fairly understandable. The code is below.

```c++
{
    auto number_of_placeholders = 0;

    for (std::size_t i = 0; i < format.size(); ++i) {
        if (format[i] == '{') {
            for (std::size_t j = i + 1; j < format.size(); ++j) {
                if (format[j] == '{') {
                    write(STDOUT_FILENO, "wrong formatting!\n\0", 19);
                    exit(EXIT_FAILURE);
                }

                if (format[j] == '}') {
                    ++number_of_placeholders;
                    break;
                }
            }
        }
    }

    if (sizeof...(Args) != number_of_placeholders) {
        write(STDOUT_FILENO, "wrong formatting!\n\0", 19);
        exit(EXIT_FAILURE);
    }

    std::string buffer = {};
    r_printf(buffer, format, args...);

    write(STDOUT_FILENO, buffer.data(), buffer.size());
}
```

## Now we do a bit of stripping
The plan is to add an argument container to `r_printf`. This way we can take one variadic argument every time, then pass the rest of variadic arguments to the `r_printf`. hence the `r_` prefix. It’s recursive. This way we don’t need to count or do anything else. Each time we do this, we strip that part that we have printed. And throw it away.

```c++
    uint placeholder_loc = format.find('{');
    buffer.append(format.data(), placeholder_loc);
    std::string new_format = format.substr(placeholder_loc + 2, format.size());
```

And at the end we make sure we either have at least one argument left or just append rest of our format to output and move away.

```c++
    if constexpr (sizeof...(args) != 0) {
        r_printf(buffer, new_format, args...);
    } else {
        buffer.append(new_format.data(), new_format.size());
    }
```
Now we do have a barebone function that just can find the place of the `{`s and strip them out. That’s it.

## Parsing arguments I guess
Sorry but no parsing in this article. I’m too lazy to write a parser from scratch. So I’m just going to use `std::to_string` and call it a day. 

The code would end up looking something like this in the end.

```c++
template <typename Arg>
    requires std::integral<Arg>
inline constexpr auto
parse_number(Arg arg)
{
    return std::to_string(arg);
}

template <typename Arg, typename... Args>
auto
r_printf(std::string& buffer, const std::string& format, const Arg& arg, const Args&... args)
{
    uint placeholder_loc = format.find('{');
    buffer.append(format.data(), placeholder_loc);

    std::string new_format = format.substr(placeholder_loc + 2, format.size());

    std::string new_arg = {};
    if constexpr (std::is_arithmetic<Arg>()) {
        new_arg = parse_number(arg);
    } else {
        new_arg = arg;
    }
    buffer.append(new_arg);

    if constexpr (sizeof...(args) != 0) {
        r_printf(buffer, new_format, args...);
    } else {
        buffer.append(new_format.data(), new_format.size());
    }
}
```

We’re using `std::is_arithmetic`. One of the new things of modern C++ which lets us find out about type arguments during compile-time. In `parse_number` I used concepts for redundancy. I don’t think it was necessary. 

Now with all of this together we can hope it works as intended!

## Benchmark results
This is the benchmark results.

```markdown
new printf took: 0.372077s for 10000 iterations
old printf took: 0.34962s for 10000 iterations
std23 print took: 0.360844s for 10000 iterations
linux write syscall took: 0.336617s for 10000 iterations
```

As you can see, libc `printf` and `write` win by far. Even though I expected `write` syscall be the fastest by a large margin, I guess flushing every time and also compiler optimizations on libc gives printf a huge advantage. Then comes iso std23 print. To be honest I didn’t expect to beat it. But we’re close and that’s good.

## Wrap up
Thank you for reading this article. If you have any suggestion or found any bug in the code, please open an issue on github. I’ll be grateful to respond. Have a great night or day at whatever time you’re reading this.

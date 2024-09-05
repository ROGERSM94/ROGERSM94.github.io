---
layout: post
title:  "Code is for people?"
date:   2024-09-05 11:50:05 -0400
categories: jekyll update
---

I think that code exists on a spectrum of "Code for people" and "Code for computers". The differentiation is in the usage,
code for people could look like:

{% highlight cpp %}

#include <project.h>

int main(int argc, char** argv) {
    Options options = parse_options(argc, argv);
    std::optional<Data> data = process_data(source);
    if (!data.has_value())
        return EXIT_FAILURE;
    try {
        persist_data(*data);
    } catch (...) {
        log_persistence_error(options);
        return EXIT_FAILURE;
    }
    return EXIT_SUCCESS;
}

{% endhighlight %}

Now this code might not be the most specific, but it's very clear what it is doing: Processing data and then persisting it.
This is code for people because it requires very little cognitive load to understand at a high level what the code is trying
to do.

Consider the following implementation of std::rotate taken from cppreference.com:

{% highlight cpp %}

template<class ForwardIt>
constexpr // since C++20
ForwardIt rotate(ForwardIt first, ForwardIt middle, ForwardIt last)
{
    if (first == middle)
        return last;
 
    if (middle == last)
        return first;
 
    ForwardIt write = first;
    ForwardIt next_read = first; // read position for when "read" hits "last"
 
    for (ForwardIt read = middle; read != last; ++write, ++read)
    {
        if (write == next_read)
            next_read = read; // track where "first" went
        std::iter_swap(write, read);
    }
 
    // rotate the remaining sequence into place
    rotate(write, next_read, last);
    return write;
}

{% endhighlight %}

At first glance, it seems like "code for people" because it is clean and well-commented but it's extremely complex and
can be hard to verify its correctness especially if you're unfamiliar with those kinds of algorithms.  This is "code for
computers" because the correctness and performance are more important than our ability to change it.  This is because
std::rotate is a crucial building block in many algorithms where small gains in efficiency could lead to very large amounts
of CPU cycles saved across numerous applications. Only a very small number of highly expert programmers would consider changing
this code so we aren't as concerned about the need to quickly change it.

Humans excel at describing what they want computers to do at a high level, like "I want to be able to share files over 
the internet." In contrast, computers are better at handling precise instructions, such as "Send this data in this
format over that socket." Recognizing whether your code is intended for people or machines can guide your approach to writing it.

So next time you're coding, consider your target audience: Is it the computer, where performance and precision are key? 
Or is it your coworker, where clarity and maintainability matter most? By keeping this in mind, you can strike the right
 balance and make better design decisions that benefit both.


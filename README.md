# About

I recently got a new M1 MacBook Air to finish off school when my 2017 15" MacBook Pro started displaying a [spicy pillow][spice]. There have been a lot of [stellar reports][Anandtech] about the M1 CPU but I couldn't find anything about how well fuzzing with the M1 fared. 

This machine was never inteded for fuzzing. I just thought it would be a neat experiment to try out. As the [AFL++ README][afl] states:
> If you just use one CPU for fuzzing, then you are fuzzing just for fun and not seriously :-)

Who doesn't like to have fun? So our question is: is the M1 faster when compared to a seventh-gen Kaby Lake 2.9GHz Quad-Core i7 (our baseline) with just one target? If so, by how much? In our analysis will look specifically at execs/second.

# Research Area and Hypothesis

We know that every run of `afl-fuzz` will utilize [one CPU core][afl-core]. We also know, from the [AndandTech][Anandtech] review, that the M1 performs favorably in single-threaded performance and it beats out Intel's eleventh-gen Tiger Lake 3.00 GHz i7. Given this, I am going to hypothesis that the M1 will yield more execs/second than the baseline. 

# Testing

## Setup

* The baseline MacBook Pro has the following specs:
    * 2.9GHz Quad-Core Intel Core i7
    * 16GB RAM
    * Radeon Pro 560 4GB
    * VMware Fusion 12.1.2
    * Ubuntu 20.04
* The shiny new 2020 MacBook Air has the following:
    * Apple M1
    * 16GB RAM
    * 8 Core GPU
    * Parallels 16.5.0
    * Ubuntu 20.04





[spice]: https://www.anandtech.com/show/16252/mac-mini-apple-m1-tested
[Anandtech]: https://www.anandtech.com/show/16252/mac-mini-apple-m1-tested
[afl]: https://github.com/AFLplusplus/AFLplusplus
[afl-core]: https://github.com/mirrorer/afl/blob/master/docs/parallel_fuzzing.txt
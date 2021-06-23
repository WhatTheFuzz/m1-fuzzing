# About

I recently got a new M1 MacBook Air to finish off school when my 2017 15" 
MacBook Pro started displaying a [spicy pillow][spice]. There have been a lot 
of [stellar reports][Anandtech] about the M1 CPU but I couldn't find anything 
about how well fuzzing with the M1 fared. 

This machine was never intended for fuzzing. I just thought it would be a neat 
experiment to try out. As the [AFL++ README][afl] states:
> If you just use one CPU for fuzzing, then you are fuzzing just for fun and 
not seriously :-)

Who doesn't like to have fun? So our question is: is the M1 faster when 
compared to a seventh-gen Kaby Lake 2.9GHz Quad-Core i7 (our baseline) with 
just one target? If so, by how much? In our analysis we will look specifically 
at execs/second.

# Research Area and Hypothesis

We know that every run of `afl-fuzz` will utilize [one CPU core][afl-core]. We 
also know, from the [Anandtech][Anandtech] review, that the M1 performs 
favorably in single-threaded performance and it beats out Intel's eleventh-gen 
Tiger Lake 3.00 GHz i7. Given this, I am going to hypothesize that the M1 will 
yield more execs/second than the baseline. 

# Testing

## Setup

* The baseline MacBook Pro has the following specs:
    * 2.9GHz Quad-Core Intel Core i7
    * 16GB RAM
    * Radeon Pro 560 4GB
    * VMware Fusion Pro 12.1.2
    * Ubuntu 20.04 AMD64
* The 2020 MacBook Air has the following:
    * Apple M1
    * 16GB RAM
    * 8 Core GPU
    * Parallels 16.5.0
    * Ubuntu 20.04 ARM64

Each VM was configured to use two processors and 4GB of RAM. On the M1, there 
is a concept of efficiency and performance cores. I am not sure how Parallels 
currently handles this. 

I don't like installing development tools on my Mac. I prefer to do all of my 
development on virtual Linux machines. The problem is that Parallels only 
allows me to install one copy on one device with my standard license. VMware 
Fusion does not yet allow for ARM virtual machines to run on the new Apple 
Silicon chips. This means that my testing will be performed on dissimilar 
virtual environments and it could be the case that the results will be skewed 
because of it. 

## Target

We chose our target `rgbasm` because it takes input from a file, the name of 
which is passed in as an argument to the executable. This makes it rather easy 
to fuzz. The source is freely available as well. We compiled it with 
instrumentation using `afl-cc`. To set this up, please view the [afl++][afl] 
repository. Compile the target with the following:

```sh
git clone https://github.com/gbdev/rgbds.git
cd rgbds
mkdir build
cmake -DCMAKE_C_COMPILER=afl-cc -DCMAKE_CXX_COMPILER=afl-c++ -B build
file build/src/rgbasm
```

The target is now compiled with instrumentation and can be fuzzed by running 
the following in the same directory as the target.

```sh
# create a test seed and the necessary folders for afl
mkdir input
mkdir output
echo "test" > input/seed0

# start the job
afl-fuzz -i input/ -o output/ -D -- ./rgbasm @@
```

# Results

After running the above on two new virtual machines, we found that the M1 
running Parallels had higher executions per second (~5,000/second vesus 
~1,200/second). Does this mean that the M1 
is definitely better at fuzzing? Not necessarily. Executions per second are not 
everything we care about, but the difference is pretty staggering. We also did 
not have the same the virtualization software for both machines. For a baseline 
machine, the MacBook Air is pretty exciting in this field. Please don't draw 
any professional conclusions from this research (though I doubt anyone out 
there is going to network a bunch of M1 Macs and start fuzzing...). I just 
thought it was neat.

## MacBook Air, M1
![afl results, arm](/resources/afl-arm.png)

## MacBook Pro, Intel 
![afl results, amd](/resources/afl-amd.png)


[spice]: https://www.anandtech.com/show/16252/mac-mini-apple-m1-tested
[Anandtech]: https://www.anandtech.com/show/16252/mac-mini-apple-m1-tested
[afl]: https://github.com/AFLplusplus/AFLplusplus
[afl-core]: https://github.com/mirrorer/afl/blob/master/docs/parallel_fuzzing.txt
# wrx - a HTTP benchmarking tool based on wrk

  This is a fork of wrk that adds the following functionality:

  - outputting the latency histogram raw data with `--histogram`
  - outputting the number of requests completed in each 250ms interval
  with `--timeseries`
  - outputting the number of requests completed in each 250ms interval
  by each thread with `--thread-timeseries`
  - calculation of 99.9 and 99.99 latency percentiles

  Example usage:

    wrx --duration=1 --threads=1 --connections=100 --latency \
    --thread-timeseries --timeseries --histogram http://localhost:3000/static

  Output:

    Starting thread 0...
    Starting thread 1...
    Running 1s test @ http://localhost:3000/static
      2 threads and 100 connections

    THREAD  |  EPOCH  |  REQUESTS
         0         0         2662
         1         0         2656
         1         1         2813
         0         1         2814
         0         2         2838
         1         2         2839
         1         3         2866
         0         3         2866
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     4.46ms  250.52us   7.90ms   92.96%
        Req/Sec    11.20k   335.21    11.51k    75.00%
      Latency Distribution
      50.00%    4.41ms
      75.00%    4.52ms
      90.00%    4.61ms
      99.00%    5.51ms
      99.90%    6.86ms
      99.99%    7.81ms
      22354 requests in 1.00s, 3.86MB read
    Requests/sec:  22344.03
    Transfer/sec:      3.86MB
    Request counts time series (interval = 250ms) 
    5318
    5627
    5677
    5732
    Latency histogram (bin width = 1us)
    0
    0
    0
    0
    0
    0
    0
    1
    4
    [...]

  The latency histogram data is outputted up to the last non-zero value. The
  output can be rather large (123k lines if max recorded latency = 123ms). 

  Background info: I have really enjoyed working with wrk, as it was
  consistently able to generate load of over 125k req/s/thread. In a recent
  project, I was attempting to measure changes in backend application
  throughput over time and hence created this fork.

  Below is the original README of wrk:

  ---

  wrk is a modern HTTP benchmarking tool capable of generating significant
  load when run on a single multi-core CPU. It combines a multithreaded
  design with scalable event notification systems such as epoll and kqueue.

  An optional LuaJIT script can perform HTTP request generation, response
  processing, and custom reporting. Details are available in SCRIPTING and
  several examples are located in [scripts/](scripts/).

## Basic Usage

    wrk -t12 -c400 -d30s http://127.0.0.1:8080/index.html

  This runs a benchmark for 30 seconds, using 12 threads, and keeping
  400 HTTP connections open.

  Output:

    Running 30s test @ http://127.0.0.1:8080/index.html
      12 threads and 400 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency   635.91us    0.89ms  12.92ms   93.69%
        Req/Sec    56.20k     8.07k   62.00k    86.54%
      22464657 requests in 30.00s, 17.76GB read
    Requests/sec: 748868.53
    Transfer/sec:    606.33MB

## Command Line Options

    -c, --connections: total number of HTTP connections to keep open with
                       each thread handling N = connections/threads

    -d, --duration:    duration of the test, e.g. 2s, 2m, 2h

    -t, --threads:     total number of threads to use

    -s, --script:      LuaJIT script, see SCRIPTING

    -H, --header:      HTTP header to add to request, e.g. "User-Agent: wrk"

        --latency:     print detailed latency statistics

        --timeout:     record a timeout if a response is not received within
                       this amount of time.

## Benchmarking Tips

  The machine running wrk must have a sufficient number of ephemeral ports
  available and closed sockets should be recycled quickly. To handle the
  initial connection burst the server's listen(2) backlog should be greater
  than the number of concurrent connections being tested.

  A user script that only changes the HTTP method, path, adds headers or
  a body, will have no performance impact. Per-request actions, particularly
  building a new HTTP request, and use of response() will necessarily reduce
  the amount of load that can be generated.

## Acknowledgements

  wrk contains code from a number of open source projects including the
  'ae' event loop from redis, the nginx/joyent/node.js 'http-parser',
  and Mike Pall's LuaJIT. Please consult the NOTICE file for licensing
  details.

## Cryptography Notice

  This distribution includes cryptographic software. The country in
  which you currently reside may have restrictions on the import,
  possession, use, and/or re-export to another country, of encryption
  software. BEFORE using any encryption software, please check your
  country's laws, regulations and policies concerning the import,
  possession, or use, and re-export of encryption software, to see if
  this is permitted. See <http://www.wassenaar.org/> for more
  information.

  The U.S. Government Department of Commerce, Bureau of Industry and
  Security (BIS), has classified this software as Export Commodity
  Control Number (ECCN) 5D002.C.1, which includes information security
  software using or performing cryptographic functions with symmetric
  algorithms. The form and manner of this distribution makes it
  eligible for export under the License Exception ENC Technology
  Software Unrestricted (TSU) exception (see the BIS Export
  Administration Regulations, Section 740.13) for both object code and
  source code.

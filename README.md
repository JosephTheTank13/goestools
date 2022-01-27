# goestools-maintained

An attempt at keeping goestools functional over time. If you encounter any problems, please create an issue and report the problem.

This fork of goestools is meant for Ubuntu users (21.10 at the time of writing) you may run into issues on raspberry pi.

* **goesrecv**: Demodulate and decode signal into packet stream.
* **goeslrit**: Assemble LRIT files from packet stream.
* **goesproc**: Process LRIT files into plain files and images.

Since it seems peitern is no longer maintaining goestools, and it's a very commonly used peice of software I decided to try to merge all the fixes and workarounds I can find into one place so people can compile goestools on Ubuntu/debian. Raspberry Pi compiling using peitern's master branch still appears to work at the time of writing this.

## Requirements

System dependencies:

* cmake
* libopencv-dev(for image processing in goesproc)
* zlib (for EMWIN decompression)(comes with Ubuntu)
* libairspy-dev (optional)
* librtlsdr-dev
* libproj-dev
* git

Bundled dependencies:

* libcorrect (currently a fork with CMake related fixes)
* libaec

## Build Instructions

``` shell
sudo apt-get install libairspy-dev librtlsdr-dev libopencv-dev libproj-dev cmake
git clone https://github.com/pietern/goestools
cd goestools
git submodule init
git submodule update --recursive
mkdir -p build
cd build
cmake ../ -DCMAKE_INSTALL_PREFIX=/usr/local
sudo make -j6 install
```

## Usage
`sudo touch goesrecv.conf` in home directory

In goesrecv.conf, paste in:

[demodulator]

mode = "hrit"

source = "rtlsdr"

[rtlsdr]

frequency = 1694100000

sample_rate = 2400000
gain = 5

bias_tee = false

[costas]

max_deviation = 200e3

[decoder.packet_publisher]

bind = "tcp://0.0.0.0:5004"

send_buffer = 1048576

[monitor]

statsd_address = "udp4://localhost:8125"

Then run:
`goesrecv -v -i 1 -c ~/goesrecv.conf`

and:
`goesproc -c /usr/local/share/goestools/goesproc-goesr.conf -m packet  --subscribe tcp://127.0.0.1:5004`
to start getting images(assuming you have a clear signal)

# for more info, go to https://github.com/pietern/goestools for the full readme.txt

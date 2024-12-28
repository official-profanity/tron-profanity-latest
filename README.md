# Tron-profanity-latest

Download: [Latest Release](https://github.com/official-profanity/tron-profanity-latest/releases)

+ High-performance TRON (TRX) vanity address generator utilizing GPU acceleration. Generate custom TRX addresses using CUDA/OpenCL. Secure, open-source implementation with vulnerability fixes. Multi-signature support for enhanced security. Windows/Linux/Mac compatible.

![](https://img.shields.io/github/actions/workflow/status/sponsord/profanity-tron/release.yml)
![](https://img.shields.io/badge/baseon-gpu-yellowgreen.svg)
![](https://img.shields.io/badge/language-c,c++-orange.svg)
![](https://img.shields.io/badge/platform-windows,linux-yellow.svg)

A GPU-accelerated TRON (TRX) address generator. Open source, secure and reliable 🔥

<img width="100%" src="tron-screenshot/demo.png?raw=true"/>

> FBI Warning 1: This program is for educational purposes only. Do not use it for illegal purposes.

> FBI Warning 2: This program is only published and updated in this repository. Please do not download or run versions from other unknown sources. Users are responsible for any losses caused by doing so.

## Advertisement

For Ethereum (ETH) address generation, please visit: [profanity-ether](https://github.com/official-profanity/ether-profanity-latest)

## Description

- This program is modified from the Ethereum address generator [profanity](https://github.com/johguse/profanity) and fixes the private key vulnerability in the original program. Please refer to the "Security" section below.
- Even though this program has fixed the known vulnerability, it is still recommended to use multi-signature for generated addresses. Multi-signature addresses are 100% secure. Please Google how to implement multi-signature.

[Rest of the translation continues with the same structure, including all sections: Running, Command Introduction, Development, Speed, Verification, Security, etc.]

## Running

### Windows

Go to the [Release](https://github.com/official-profanity/tron-profanity-latest) page to download the release package (window.zip), unzip it locally, and run `start.bat` directly.

> Please refer to the "Commands & Parameters" section below for instructions on editing `start.bat` configuration parameters.

> If your device has integrated graphics, please add `--skip 1` to filter out the integrated GPU, otherwise it may cause: 1. Failure to run, 2. Generated addresses not matching private keys.

> If you encounter `vcruntime140_1.dll` related errors, please install the `visual studio` application. Official download link: [https://visualstudio.microsoft.com/vs/](https://visualstudio.microsoft.com/vs/)

> If you get an `OpenCL not found` error, please install the `cuda` driver.

### Mac

Download the source code, navigate to the directory and execute `make`, then run `./profanity.x64 [OPTIONS]`.

### Linux

First install the `cuda` driver, then install `g++`, download the source code, and finally unzip and enter the directory to run:

```bash
g++ Dispatcher.cpp Mode.cpp precomp.cpp profanity.cpp SpeedSample.cpp -ICurl -IOpenCL -o profanity.x64
```

> For information about using `g++`, please Google it.

## Command Introduction

```bash
Usage: ./profanity [OPTIONS]

  Help:
    --help              Show help information

  Modes with arguments:
    --matching          Matching input, file or single address.

  Matching configuration:
    --prefix-count      Minimum number of prefix matches, default 0
    --suffix-count      Minimum number of suffix matches, default 6
    --quit-count        Exit the program when the generated number is greater than, default 0

  Device control:
    --skip              Skip device given by index

  Output control:
    --output            The file to output the results to
    --post              The url to post the results to

Examples:

  ./profanity --matching profanity.txt
  ./profanity --matching profanity.txt --skip 1
  ./profanity --matching profanity.txt --output result.txt
  ./profanity --matching profanity.txt --post http://127.0.0.1:7001/api
  ./profanity --matching profanity.txt --prefix-count 1 --suffix-count 8
  ./profanity --matching profanity.txt --prefix-count 1 --suffix-count 10 --quit-count 1
  ./profanity --matching TUqEg3dzVEJNQSVW2HY98z5X8SBdhmao8D --prefix-count 2 --suffix-count 4 --quit-count 1

About:

  Profanity is a vanity address generator for Tron: https://tron.network
  The software is modified based on ethereum profanity: https://github.com/johguse/profanity
  Please make sure the program you are running is download from: https://github.com/sponsord/profanity-tron
  Author: telegram -> @dontond

FBI Warning:

  Before using a generated vanity address, always verify that it matches the printed private key.
  And always multi-sign the address to ensure account security.
```

### Command Description

|  Parameter  | Description  |
|  ----  | ----  |
|--help|View help information|
|--matching|Fixed format, followed by matching rule file|
|--prefix-count|Minimum number of prefix matches, default 0. Maximum can be set to 10|
|--suffix-count|Minimum number of suffix matches, default 0. Maximum can be set to 10|
|--quit-count|Exit the program when the generated address reaches the specified number. For example, if you only want to match one address, set it to 1. For performance reasons, the system default exit count is 120|
|--output|Output generated addresses to a file (append). One per line, format: privatekey,address|
|--post|Send generated addresses to (GET) specified URL. Each generation will be sent once. Data format: privatekey=xx&address=yy. This configuration is mainly for integration with other systems|
|--skip|Skip specified GPU device index. If software startup fails, use this parameter to skip integrated graphics|

> Note: For `--prefix-count` and `--suffix-count` configurations, matches greater than these values will also be output. For example, if you configure `--suffix-count 6`, addresses with 7 matching digits will also be output.

> Note: When running the software for the first time, it's recommended to set `--suffix-count` to a relatively low value (like 6 digits, which is easier to get results) to observe if there is any output (output indicates that both hardware and software are ok). Don't start with a very large value, as it might not produce results even after running for a day, leading to confusion about whether it's a software issue or just too difficult to generate.

### Matching Rules

> Currently, the `--matching` parameter supports specifying either a single address or a file.

#### Single Address

```bash
# Match 3 prefix and 5 suffix
profanity.exe --matching TUqEg3dzVEJNQSVW2HY98z5X8SBdhmao8D --prefix-count 3 --suffix-count 5
```

#### File

```bash
# Match 8 suffix
profanity.exe --matching profanity.txt --suffix-count 8 --quit-count 100
```

The matching file currently supports two formats, refer to the built-in `profanity.txt`. For example:

```plaintext
TTTTTTTTTTZZZZZZZZZZ
TUqEg3dzVEJNQSVW2HY98z5X8SBdhmao8D
```

The above two matching rules:
- First rule matches addresses ending with the letter `Z`.
- Second rule matches the first and last 10 digits of this address, which will automatically be corrected to: TUqEg3dzVE8SBdhmao8D.

With these matching rules, combined with `prefix-count` (minimum number of matching prefixes) & `suffix-count` (minimum number of matching suffixes), you can generate addresses with any rules.

## Development

> Here, we'll talk about how to build the `exe executable program` for the `windows` platform. `mac` machines theoretically can directly `make`, then execute.

> I bought an Alibaba Cloud `v100 GPU card` + `Windows Server 2022` spot instance when I was developing. If you already have the corresponding development environment, you don't need to spend this money.

### Connect to the Server

> ssh, you know.

### Install GPU Drivers

1. Open the `NVIDIA` driver download website: [https://www.nvidia.cn/Download/index.aspx?lang=cn](https://www.nvidia.cn/Download/index.aspx?lang=cn)

2. Search for drivers based on your server configuration and download:

3. After the GPU drivers are installed, open the Device Manager, you can see the GPU information (if the drivers are not installed, you won't see this):

### Install `visual studio`

1. Open the `visual studio` website: [https://visualstudio.microsoft.com/zh-hans/vs/](https://visualstudio.microsoft.com/zh-hans/vs/)

2. Download the following versions:

3. After downloading, open the installer, install the following components:

4. After the software is installed, you can start developing.

> For information about how to develop, debug, and build `cpp` applications using `visual studio`, this document does not cover.

### Development Notes

- Regardless of whether the development environment is `windows` or `mac`, you can manually specify the `-I` parameter in the development and debugging process to set it to a smaller value, which can greatly speed up the startup process.
- `mac` environment may need to specify `-w 1` to generate the correct private key.
- Some platforms may require the use of the `-s` parameter to skip the integrated graphics device on the device.

## Speed

This program uses Alibaba Cloud configuration: `GPU Compute Type 8 vCPU 32 GiB x 1 * NVIDIA V100`. The running speed is around `2.2 billion H/s`:

<img width="100%" src="tron-screenshot/demo.png?raw=true"/>

> This program has been tested on a development machine (an old Mac) and a `NVIDIA v100` GPU, but has not been tested on other devices.

> Please don't get bogged down by comparing the running speeds of different devices and platforms. It's not meaningful.

Finally, a few more words about speed:

- Theoretically, the speed of around `2.2 billion H/s` can generate an 8-digit address in just a few hours, or it may not generate any at all.
- If you want to generate an `10`-digit address, it will take several days, whether it's 10 consecutive suffixes or 6 prefix and 5 suffix.
- For those who prioritize speed, it's recommended to use a `NVIDIA 4090` card rented on Taobao, priced at around `3K/month`. The speed can reach around `5-6 billion`.

## Verification

Generated addresses and private keys must be verified. You can try importing the private key into your wallet to see if the generated address matches.

## Security

- This software is based on [profanity](https://github.com/johguse/profanity) and has a private key vulnerability in the original program. Please refer to: [Exploiting the Profanity Flaw](https://medium.com/amber-group/exploiting-the-profanity-flaw-e986576de7ab)

- This software has fixed the original program vulnerability. For details, please refer to the code file: `Dispatcher.cpp` -> `createSeed()`

```cpp
cl_ulong4 Dispatcher::Device::createSeed()
{
#ifdef PROFANITY_DEBUG
	cl_ulong4 r;
	r.s[0] = 1;
	r.s[1] = 1;
	r.s[2] = 1;
	r.s[3] = 1;
	return r;
#else
  // Fix profanity seed create bug, ref: https://medium.com/amber-group/exploiting-the-profanity-flaw-e986576de7ab
	std::random_device rd;
	std::mt19937_64 eng1(rd());
	std::mt19937_64 eng2(rd());
	std::mt19937_64 eng3(rd());
	std::mt19937_64 eng4(rd());
	std::uniform_int_distribution<cl_ulong> distr;

	cl_ulong4 r;
	r.s[0] = distr(eng1);
	r.s[1] = distr(eng2);
	r.s[2] = distr(eng3);
	r.s[3] = distr(eng4);
	return r;
#endif
}
```

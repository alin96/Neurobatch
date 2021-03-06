# Neurobatch v1.1
Alpha Release

**Note from the Author:**
This script aims to remove the technical aspect of file retrieval from large database from neurosynth. For those using this tool with the purpose to enrich and educate our world,  I hope you find this useful and I wish you the best.

This Python3 script is specific for [neurosynth](http://neurosynth.org/) and allows:

1. Batch extract download links "Functional Connectivity"
2. Batch extract download links "Meta-analytic Coactivation"
3. Batch download "Functional Connectivity" files
4. Batch download "Meta-analytic Coactivation" files

```
usage: neurobatch.py [-h] -i FILE [-o FILE] [-s] [-f] [-m] [-a WORKERS] [-w SEC SEC]

Batch Downloader and Link Extractor for http://neurosynth.org
GitHub: https://github.com/alin96/Neurobatch

optional arguments:
  -h, --help  show this help message and exit
  -i FILE     csv file path
  -o FILE     output folder path
  -s          set flag to skip link collection
  -f          download functional connectivity
  -m          download meta-analytic coactivation
  -a WORKERS  multithread mode, if not set default: 1
  -w SEC SEC  random wait time lower and upper bounds inclusive
```
the usage of the flags are explained in the [Example Usage](#example-usage) Section

**IMPORTANT** This script doesn't check if the system has enough memory. Each Functional Connectivity file is 1.1 MB large and each Meta-analytic Coactivation file is variable. Before running the script, take into account how much memory is needed to avoid errors.

After running the script, your input csv file will have Functional Connectivity download links in column 4 and Meta-analytic Coactivation download links in column 5.

## Dependencies:
Download and install the latest version of [Python 3](https://www.python.org/downloads/)

## Expected Input:
sample.csv

 -56 |14  | 10|
--- | --- | ---
10 | -2 | 4
24 | -2 | 56
-6 | 16 | 42

**NOTE** that there should not be any headers

## Example Usage:
**IMPORTANT** for Mac OSx Users:
After installing Python 3, use `python3` to run the script such as:
```
python3 neurobatch.py -f -m -i sample.csv -o output/ -w 2 6
```
instead of the regular `python` command which is seen below

### Basic:
```
python neurobatch.py -f -m -i sample.csv -o output/ -w 2 6
```
This is the most basic usage one thread will collect all download links and save them in the input csv (`sample.csv` in this case). Setting the `-f` flag will trigger the script's functional connectivity file download function, and the `-m` flag will trigger the script's meta-analytic coactivation file download function. Set either `-f` or `-m` to download only one type or both can be set at the same time to trigger both file download types. The `-w` flag must be set when the `-o` flag is set to denote delay time between downloads. During file downloads, a random wait time between the two numbers inputted will be chosen. Set `-w 0 0` if no delay time is wanted. All functional connectivity files and meta-analytic coactivation files will be saved in a subdirectory named `fc` and `mc` respectively in the directory set by `-o`.
**NOTE** This method is usually extremely slow. Visit the [Advance](#advance) section for details on how to download multiple files simultaneously 

```
python neurobatch.py -i sample.csv
```
This usage only extracts download links and saves them in the input csv file. The `-w` tag will be ignored. This script command is ideally followed by the second example under [Advance](#advance)
### Advance:
```
python neurobatch.py -a 10 -f -m -i sample.csv -o output/ -w 2 6
```
Setting the `-a` flag unleashes the power of multithreading for concurrent batch downloading. Downloads will now be handled with the set number of threads. The number of threads signifies how many files will be downloaded asynchronously (number of threads equals how many files will be downloaded concurrently). Note that each thread will have its own individual wait time. The `-w` does not guarantee non-overlapping, sequential download requests in this mode. Not setting the `-a` flag will run the script with the default 1 thread.

```
python neurobatch.py -f -m -a 5 -s -i sample.csv -o output/ -w 2 6
```
Setting the `-s` flag will skip download link extraction and will assume the input csv file will supply the download link in the 4th column. This command can be ideally used after the second basic command `neurobatch.py -i sample.csv`, which will be the same as the full command `neurobatch.py -a 10 -i sample.csv -o output/ -w 2 6`

## Example Output:
```
alin$ python3 neurobatch.py -a 10 -m -i sample.csv -o output/ -w 2 6

Loaded coordinates into Memory

Collecting 11 Download Links
Progress: [########################################] 100.00%
Completed in 0.485 s

Done collecting download links

Downloading 11 Meta-analytic Coactivation files with 10 worker(s)
Progress: [########################################] 100.00%
Completed in 26.882 s

All meta-analytic coactivation files downloaded successfully
```
Setting the `-a 5` will download 5 files concurrently. Setting the `-m` downloads only Meta-analytic Coactivation files. Setting the `-o output/` will put all files under the a folder in the directory of the script named "output". Setting `-w 2 6` tells each download to wait anywhere from 2 to 6 seconds before downloading the file 

# "Quantitative Economics with Julia":

## About this Repository 

This is the source repository for [Quantitative Economics with Julia](https://lectures.quantecon.org/jl).  These instructions required for authorig/editing the textbook and notebooks, and are not necessary for typical usage.

See `LICENSE.md` for licensing and copyright information. 

## Usage

### Prerequisities

* The latest `quantecon/jupinx` Docker image (see the **Containerization** section), or: 
0. Start within your home directory, using [WSL](https://github.com/ubcecon/cluster_tools/blob/master/WSL.md#install-wsl-from-ubuntu-and-conda) if on Windows. 
1. Ensure key dependencies are installed
```bash
sudo apt install make
sudo apt-get install libxt6 libxrender1 libgl1-mesa-glx libqt5widgets5
```
2. Install Conda
```bash
wget https://repo.anaconda.com/archive/Anaconda3-2019.07-Linux-x86_64.sh
bash Anaconda3-2019.07-Linux-x86_64.sh
```
3. Install Julia
```bash
wget https://julialang-s3.julialang.org/bin/linux/x64/1.2/julia-1.2.0-linux-x86_64.tar.gz
tar -xzvf julia-1.2.0-linux-x86_64.tar.gz
```

4. Install Jupinx
```bash
pip install jupinx
```

5. Assuming you installed anaconda in your home directory (for `USERNAME`) then,
- Within your home directory, `edit .bashrc`.  This opens Vim.  Go to the bottom of the file, and type `i` to enter insert mode.
- Add something like the following:
```bash
export PATH=/home/USERNAME/anaconda3/bin:/home/USERNAME/julia-1.1.0/bin:$PATH
```
- Hit `<Esc>` to exit insert mode, and then type `:x` to save and exit.

6. If you are using WSL, then it can be useful to add a symlink.  To do this from your home directory, with your Windows username as `WINDOWSUSERNAME`, do something like the following
```bash
ln -s /mnt/c/Users/WINDOWSUSERNAME/Documents/GitHub Documents
```
Restart ubuntu or WSL

It's recommended that you install and precompile the packages used by the lectures **before** building. To do this: 

1. (Optional) Delete your `~/.julia` folder to start fresh.

2. `cd` to the `source/rst` folder in this repo. In a Julia REPL (i.e. `julia` in terminal if your `.bashrc` was edited above), run
```julia
] add InstantiateFromURL IJulia; precompile`. 
```
3. Then (verifying you are in the `/lecture-source-jl/source/rst` diirectory),
```julia
] activate .; instantiate; precompile
```
This will take a long time to run.  You can safely ignore build errors for `Electron`
 
### Building

There are a few different targets, notably: 

* `make website`, which will generate Jupyter, execute notebooks, and then turn that into HTML 

* `make coverage`, which will do steps (1) and (2) above (**with otherwise hidden unit tests**), and then generate a report about which notebooks fail. 

* `make preview`, which will do steps (1), (2), and (3) above and then fire up a local HTTP server. 

### Options and Special Cases

Specifying parallel execution (i.e., `make coverage -e parallel=True`) will use 4 cores instead of 1. This leads to a notable speedup in build times. 

You can build only a few notebooks by [**FILL IN PROCEDURE HERE**]

### Containerized Build

Alternately, you can use the `quantecon/jupinx` docker image, which has all these dependencies baked in. 

The advantage of a containerized setup is that you can use a siloed, "pre-baked" setup environment to build the lectures. 

0. Install [Docker](https://www.docker.com/).

1. Run `docker pull quantecon/jupinx`. 

2. In a terminal, cd to this repository, and run `docker run --name quantecon-docker -it -d -v "$(pwd)":/home/jovyan/work quantecon/jupinx` from inside the directory (Linux/macOS). It should spit out a container ID string then exit. Try `${PWD}` on Windows, but your mileage may vary. 

     :warning: In order to guarantee reproducibility, you should either be mounting a fresh clone of this repository, or sanitize things by running `git clean -xdff` (remove uncommitted/untracked files) and `git reset --hard` (reset to the last git state.) Otherwise, local variance in the mounted files may impact your results.

3. In the same terminal (i.e., not inside the container), run `docker exec quantecon-docker bash -c "cd work && make jupyter".` Change it to `jupyter-tests` if you want it to output/execute the test blocks. 

4. Grab a coffee. The Julia side executes in serial, so it takes about an hour (modulo your processor speed.)

5. After it's done, in a terminal run `docker stop quantecon-docker` and `docker rm quantecon-docker`. This will garbage-collect the container, and free the name `quantecon-docker` for your next run. If you're having trouble, run `docker rm -f quantecon-docker` to force removal. 


[Home](README.md) | [Previous - Examples of using DeepSpeech](EXAMPLES.md)

# Continuous Integration with DeepSpeech

- [Continuous Integration with DeepSpeech](#continuous-integration-with-deepspeech)
  * [An introduction to GitHub Actions](#an-introduction-to-github-actions)
  * [Key concepts and how they relate to files in the DeepSpeech GitHub repo](#key-concepts-and-how-they-relate-to-files-in-the-deepspeech-github-repo)
    + [Workflows](#workflows)
    + [Events](#events)
    + [Jobs, Steps and Actions](#jobs--steps-and-actions)
    + [Runners](#runners)
  * [Running continuous integration from GitHub](#running-continuous-integration-from-github)

_This section of the PlayBook assumes that you have [forked](https://docs.github.com/en/github/getting-started-with-github/fork-a-repo) DeepSpeech and wish to customize it for your own purposes. The intent of this section is to provide guidance on setting up [continuous integration](https://en.wikipedia.org/wiki/Continuous_integration) using [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions)._

## An introduction to GitHub Actions

DeepSpeech uses GitHub Actions for continuous integration (CI). We recommend that you read through [this introduction to GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions) before proceeding to the rest of the document.

## Key concepts and how they relate to files in the DeepSpeech GitHub repo

This section outlines the key concepts of GitHub Actions, and relates the concepts to files you will see in the DeepSpeech GitHub repository.

### Workflows

_Workflows_ are comprised of one or more _Jobs_. _Workflows_ can be scheduled to execute at a particular time, or can be triggered by an _Event_.

In DeepSpeech, _Workflows_ are defined in the `.github` directory within the `DeepSpeech` directory:

```
~/DeepSpeech/.github$ ls
total 20
4 drwxrwxr-x  4 root root 4096 Apr 20 22:52 ./
4 drwxrwxr-x 16 root root 4096 Apr 20 22:52 ../
4 drwxrwxr-x 16 root root 4096 Apr 20 22:52 actions/
4 -rw-rw-r--  1 root root 1153 Feb  1 23:58 lock.yml
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 workflows/
```

As of the time of writing, the following _Workflows_ had been defined for DeepSpeech:
```
~/DeepSpeech/.github/workflows$ ls
total 72
 4 drwxrwxr-x 2 root root  4096 Apr 20 22:52 ./
 4 drwxrwxr-x 4 root root  4096 Apr 20 22:52 ../
56 -rw-rw-r-- 1 root root 55439 Apr 20 22:52 build-and-test.yml
 4 -rw-rw-r-- 1 root root   786 Apr 20 22:52 docker.yml
 0 -rw-rw-r-- 1 root root     0 Apr 20 22:52 .git-keep-empty-folder
 4 -rw-rw-r-- 1 root root  1047 Apr 20 22:52 lint.yml
```

_Workflows_ are defined using the [YAML](https://yaml.org/) scripting language. Each _Workflow_ specifies an _Event_ on which the _Workflow_ should run, and the _Jobs_ that should be run as part of the _Workflow_.

### Events

An _Event_ is an activity. Examples of activities might be a `push` event, when a developer does a `git push` to the `DeepSpeech` repository. Other events include when a developer does a `git commit` to the repository, or when a developer opens a new `pull request` or `issue`.

_Events_ are specified in a _Workflow_. For example, in `DeepSpeech`, most of the _Workflows_ are triggered by `git push` events:

```
~/DeepSpeech/.github/workflows$ cat build-and-test.yml
name: "Builds and tests"
on:
  pull_request:
  push:
    branches:
      - master
```

In this example, which is an exerpt from the `build-and-test.yml` file, the YAML script is specifying that the _Workflow_ should only run on two _Events_; on `pull requests` and on `push` events. `push` events are specified in more detail, and in this example, the _Workflow_ will only run on `push` events on the `master` branch of the repository - and not, say, on a working branch.

### Jobs, Steps and Actions

A _Workflow_ contains many _Jobs_. _Jobs_ can execute in parallel, or can be configured to execute sequentially if needed. A _Job_ is a collection of _Steps_ that are executed as a collection. Each _Step_ can run commands or _Actions_, and _Steps_ can share data with each other. _Actions_ are "pre-built" _Steps_ that other GitHub users have created.

An example of a _Job_ within a `DeepSpeech` _Workflow_ (the `build-and-test.yml` _Workflow_) is shown below. You can see it has several _Steps_, some of which are commands, and some of which are _Actions_.

```
jobs:
  # Linux jobs
  swig_Windows_crosscompiled:
    name: "Lin|Build SWIG for Windows"
    runs-on: ubuntu-20.04
    env:
      CFLAGS: "-static-libgcc -static-libstdc++"
      CXXFLAGS: "-static-libgcc -static-libstdc++"
    steps:
      - run: |
          sudo apt-get install -y --no-install-recommends autoconf automake bison build-essential mingw-w64
      - uses: actions/checkout@v2
        with:
          repository: "lissyx/swig"
          ref: "fec7d5d3179833e37759ffc6532f86344982e26a"
      - run: |
          mkdir -p build-static/
      - run: |
          curl -sSL https://ftp.pcre.org/pub/pcre/pcre-8.43.tar.gz > pcre-8.43.tar.gz
          ./Tools/pcre-build.sh --host=x86_64-w64-mingw32
      - run: |
          sh autogen.sh
          ./configure --host=x86_64-w64-mingw32 \
            --prefix=`pwd`/build-static/ \
            --program-prefix=ds-
      - run: |
          make -j
      - run: |
          make install
      - uses: actions/upload-artifact@v2
        with:
          name: ${{ github.job }}
          path: ${{ github.workspace }}/build-static/
```

The _Actions_ specific for `DeepSpeech` are housed in the `.github/actions` directory of the repository:

```
~/DeepSpeech/.github/actions$ ls
total 64
4 drwxrwxr-x 16 root root 4096 Apr 20 22:52 ./
4 drwxrwxr-x  4 root root 4096 Apr 20 22:52 ../
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 build-tensorflow/
4 drwxrwxr-x  3 root root 4096 Apr 20 22:52 check_artifact_exists/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 get_cache_key/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 host-build/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 install-python-upstream/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 node-build/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 numpy_vers/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 package/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 package-tensorflow/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 python-build/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 run-tests/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 select-xcode/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 setup-tensorflow/
4 drwxrwxr-x  2 root root 4096 Apr 20 22:52 win-install-sox/
```

Within each of these directories is a `.yml` file that defines an _Action_. Each _Action_ calls a `bash` script. The `bash` scripts are held in the `ci_scripts` directory in the `DeepSpeech` repository:

```
~/DeepSpeech/ci_scripts$ ls
total 164
 4 drwxrwxr-x  2 root root  4096 Apr 20 22:52 ./
 4 drwxrwxr-x 16 root root  4096 Apr 20 22:52 ../
 4 -rwxrwxr-x  1 root root  3678 Apr 20 22:52 all-utils.sh
 4 -rwxrwxr-x  1 root root  2472 Apr 20 22:52 all-vars.sh
24 -rwxrwxr-x  1 root root 23668 Apr 20 22:52 asserts.sh
 4 -rwxrwxr-x  1 root root  1300 Apr 20 22:52 build-utils.sh
 4 -rwxrwxr-x  1 root root   382 Apr 20 22:52 cpp-bytes-tests.sh
 4 -rwxrwxr-x  1 root root   488 Apr 20 22:52 cpp-tests-prod.sh
 4 -rwxrwxr-x  1 root root   345 Apr 20 22:52 cpp-tests.sh
 4 -rwxrwxr-x  1 root root   428 Apr 20 22:52 cpp_tflite_basic-tests.sh
 4 -rwxrwxr-x  1 root root   569 Apr 20 22:52 cpp_tflite-tests-prod.sh
 4 -rwxrwxr-x  1 root root   541 Apr 20 22:52 cpp_tflite-tests.sh
 4 -rwxrwxr-x  1 root root   318 Apr 20 22:52 cppwin-tests.sh
 4 -rwxrwxr-x  1 root root   467 Apr 20 22:52 cppwin_tflite-tests.sh
 4 -rw-rw-r--  1 root root   316 Apr 20 22:52 docs-requirements.txt
 4 -rwxrwxr-x  1 root root   602 Apr 20 22:52 electronjs-tests-prod.sh
 4 -rwxrwxr-x  1 root root   384 Apr 20 22:52 electronjs-tests.sh
 4 -rwxrwxr-x  1 root root   631 Apr 20 22:52 electronjs_tflite-tests-prod.sh
 4 -rwxrwxr-x  1 root root   523 Apr 20 22:52 electronjs_tflite-tests.sh
 4 -rwxrwxr-x  1 root root   538 Apr 20 22:52 host-build.sh
 4 -rwxrwxr-x  1 root root   556 Apr 20 22:52 node-tests-prod.sh
 4 -rwxrwxr-x  1 root root   343 Apr 20 22:52 node-tests.sh
 4 -rwxrwxr-x  1 root root   591 Apr 20 22:52 node_tflite-tests-prod.sh
 4 -rwxrwxr-x  1 root root   482 Apr 20 22:52 node_tflite-tests.sh
 4 -rwxrwxr-x  1 root root   614 Apr 20 22:52 package.sh
 4 -rwxrwxr-x  1 root root  2787 Apr 20 22:52 package-utils.sh
 4 -rwxrwxr-x  1 root root   521 Apr 20 22:52 python-tests-prod.sh
 4 -rwxrwxr-x  1 root root   274 Apr 20 22:52 python-tests.sh
 4 -rwxrwxr-x  1 root root   502 Apr 20 22:52 python_tflite-tests-prod.sh
 4 -rwxrwxr-x  1 root root   413 Apr 20 22:52 python_tflite-tests.sh
 4 -rwxrwxr-x  1 root root  2688 Apr 20 22:52 tf-build.sh
 4 -rwxrwxr-x  1 root root  2384 Apr 20 22:52 tf-package.sh
 4 -rwxrwxr-x  1 root root  3371 Apr 20 22:52 tf-setup.sh
12 -rwxrwxr-x  1 root root  8375 Apr 20 22:52 tf-vars.sh
```

### Runners

A _Runner_ is a server that listens for available _Jobs_ to run. The _Runner_ runs each _Job_ one at a time and then reports the results back to GitHub. A _Runner_ can be installed on a cloud server, or you can use the _Runner_ hosted by GitHub.

If you are building DeepSpeech on particular hardware, or a non-Ubuntu variant of Linux (such as Arch), then you will need to [host your own _Runner_](https://docs.github.com/en/actions/hosting-your-own-runners).

## Running continuous integration from GitHub

In this example, we're going to assume that you will use a _Runner_ hosted on GitHub to perform continuous integration.

This example assumes you have cloned DeepSpeech to the location:

`https://github.com/{USERNAME}/DeepSpeech/`

1. Navigate to your GitHub repository's Settings -> Actions. This should be located at `https://github.com/{USERNAME}/DeepSpeech/settings/actions`. It's a good idea for security to change the default Action Permissions from "Allow all actions" to "Allow local actions only".

2. Next, navigate to your GitHub repository's Actions. This should be located at `https://github.com/{USERNAME}/DeepSpeech/actions`. You should see three _Workflows_ already set up. GitHub recognises these from the files stored in the `.github/workflows` directory. The _Workflows_ are set up to execute on a `push` event.

3. So, to test that they are working, we need to make a change to the `DeepSpeech` repository to trigger a `push` event. Navigate to the repository's Code listing. This should be at `https://github.com/{USERNAME}/DeepSpeech`. Add a file called `test-workflow.txt` containing dummy text and commit the change. This should trigger a `push` event, and the _Workflows_ will activate. You can monitor the _Workflows_ executing from the repository's Actions page. This is at `https://github.com/{USERNAME}/DeepSpeech/actions`. The _Workflow_ takes around 4hrs 15mins to run using the built-in GitHub Runner.

[Home](README.md) | [Previous - Examples of using DeepSpeech](EXAMPLES.md)

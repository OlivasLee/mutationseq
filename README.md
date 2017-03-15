### About
mutationSeq is software for somatic SNV detection using next generation sequencing (NGS) data from matched tumour/normal samples. It uses
a feature-based classifier trained on validated somatic mutation samples while benefiting from other available information such as base quality,
mapping quality, strand bias and tail distance.

### Installing MutationSeq

MutationSeq can be downloaded from http://compbio.bccrc.ca/software/mutationseq or https://bitbucket.org/shahlabbcca/mutationseq. Once you have downloaded it, extract it:

```{bash}
tar -xzvf museq_4.3.8.tar.gz
```

This will extract the content into a folder `mutationseq`. We will move the files into `$HOME/usr/museq/4.3.8` for organization purposes:

```
mkdir -p $HOME/usr/museq/4.3.8
mv mutationseq/* $HOME/usr/museq/4.3.8
```

Now we need to install MutationSeq. MutationSeq requires python (v2.7) and several key package dependencies. 

* numpy
* scipy
* matplotlib
* scikit-learn
* intervaltree

The best way to install all of this is to use either [Miniconda](http://conda.pydata.org/miniconda.html) or anaconda. We will use miniconda here. First download miniconda (for python 2.7) and then run:

```{bash}
sh Miniconda2-latest-Linux-x86_64.sh
```

Then follow the instructions. When you have finished following the instructions, you should have python installed:

```{bash}
which python
~/miniconda2/bin/python
```

Now we can install the dependencies needed (NOTE: Versions and specified to ensure compatibility)

```{bash}
conda install -c bioconda numpy=1.7.1 scipy=0.12.0 scikit-learn=0.13.1 matplotlib=1.2.1 intervaltree
```

One last thing that is needed before we can install MutationSeq is the Boost C libraries. We only need to download them from http://www.boost.org/. Once you have downloaded (tested on 1.51) just extract them to a location. For example, you could put it into `$HOME/usr/boost/1.51`

Once this has been installed, we can now proceed to compiling a dependency `pybam.so`.

```{bash}
make PYTHON=python BOOSTPATH=$HOME/usr/boost/1.51 -B
```

Now when you run:

```{bash}
python $HOME/usr/museq/4.3.8/museq/classify.py --version
4.3.8
```

This indicates that you have successfully installed MutationSeq.

> An important thing to note is that MutationSeq comes bundled with a trained classifer using the [scikit-learn](http://scikit-learn.org/) library and packaged as a pickle. The pickle is only compatible with the specific scikit-learn version that it was built with. As we are using miniconda here with scikit-learn version 0.13.1, we will have to use the corresponding models associated with that version. See [Calling Variants - Using MutationSeq](#using-mutationseq) for more details.


### Using MutationSeq

To call variants using MutationSeq, we use the following command:

```
mkdir -p museq/results; \
python $(HOME)/usr/museq/4.3.8/museq/classify.py \
  normal:bam/HCC1395_exome_normal.sort.markdup.17.7MB-8MB.bam \
  tumour:bam/HCC1395_exome_tumour.sort.markdup.17.7MB-8MB.bam \
  reference:refs/GRCh37-lite.fa \
  model:$(HOME)/museq/4.3.8/museq/models_anaconda/model_v4.1.2_anaconda_sk_0.13.1.npz \
  -c $(HOME)/usr/museq/4.3.8/museq/metadata.config \
  -o museq/results/HCC1395_exome_tumour_normal_17.vcf
```

Notice how for the `model` parameter, we used the `$(HOME)/museq/4.3.8/museq/models_anaconda/model_v4.1.2_anaconda_sk_0.13.1.npz`. As mentioned in the [Installing MutationSeq](#installing-mutationseq) section, this model specifically works with conda and the scikit-learn library (v0.13.1) which is how we installed MutationSeq in the workshop. If you are not using conda, then you will have to use the model version which is compatible with the scikit-learn library you have installed for your python.


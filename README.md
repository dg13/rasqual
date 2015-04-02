# RASQUAL
RASQUAL (Robust Allele Specific Quantification and quality controL) maps QTLs using population and AS signals for any sequenced based cellular tratis.

## How to build & install

Please make sure CLAPACK is installed in your environment.  Otherwise you can get the library at http://www.netlib.org/clapack/.  To build and install RASQUAL, firstly go to the source directory, then set environment variables appropriately to point the CLAPACK library as follows.  Finally use "make" to build and install RASQUAL which will be installed in "$RASQUALDIR/bin".

	RASQUALDIR=/path/to/rasqualdir/
	cd $RASQUALDIR/src
	export CFLAGS="-I/nfs/users/nfs_n/nk5/team170/Natsuhiko/CLAPACK-3.1.1.1/INCLUDE -I/nfs/users/nfs_n/nk5/team170/Natsuhiko/CLAPACK-3.1.1.1/F2CLIBS"
	export LDFLAGS="-L/nfs/users/nfs_n/nk5/team170/Natsuhiko/CLAPACK-3.1.1.1 -L/nfs/users/nfs_n/nk5/team170/Natsuhiko/CLAPACK-3.1.1.1/F2CLIBS"
	make
	make install

## Data preparation

You can find an example expression data for C11orf21 and TSPAN32 genes in the "data" directory.  There are two files: read count table (Y.txt) and sample specific offset data (K.txt), both has to be organized feature by sample format (i.e., row: gene; col: sample).  The following R script allows you to convert the count and offset files (text) into binary format used in RASQUAL software.  The script essentially converts a table data into a vector of values (double precision).

	cd $RASQUALDIR
	export RHOME=/software/R-3.0.0/bin/
	$RHOME/R --vanilla --quiet --args data/Y.txt data/K.txt < R/txt2bin.R > log

## Mapping example eQTLs

Mapping QTLs is very straightforward.  You just need to prepare a VCF file with AS counts at all SNP loci as standard input for RASQUAL.  RASQUAL also requires feature start(s) and end(s) (i.e., union of exons in this example) as inputs (-s and -e respectively).  To save memory usage, you are also required to count the number of tested SNPs (-l) and feature SNPs (-m) a priori.

	tabix data/chr11.gz 11 | bin/rasqual -y data/Y.bin -k data/K.bin -n 24 -j 1 -l 409 -m 63 \
                   -s 2316875,2320655,2321750,2321914,2324112 -e 2319151,2320937,2321843,2323290,2324279 -z -t -f C11orf21
	tabix data/chr11.gz 11 | bin/rasqual -y data/Y.bin -k data/K.bin -n 24 -j 2 -l 409 -m 61 \
                   -s 2323227,2323938,2324640,2325337,2328175,2329966,2330551,2331219,2334884,2335715,2338574,2339093 \
                   -e 2323452,2324188,2324711,2325434,2328220,2330040,2330740,2331248,2334985,2337897,2338755,2339430 \
                   -z -t -f TSPAN32

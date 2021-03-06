Time series discretization with SAX
====
[![Build Status](https://travis-ci.org/jMotif/SAX.svg?branch=master)](https://travis-ci.org/jMotif/SAX) [![codecov.io](http://codecov.io/github/jMotif/SAX/coverage.svg?branch=master)](http://codecov.io/github/jMotif/SAX?branch=master) [![Maven Central](https://maven-badges.herokuapp.com/maven-central/net.seninp/jmotif-sax/badge.svg)](https://maven-badges.herokuapp.com/maven-central/net.seninp/jmotif-sax)
[![License](http://img.shields.io/:license-gpl2-green.svg)](http://www.gnu.org/licenses/gpl-2.0.html)

This code is released under [GPL v.2.0](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html) and implements in Java:
  * Symbolic Aggregate approXimation (i.e., SAX) toolkit stack [1]
  * a simple function for time series motif discovery [2]
  * HOT-SAX - a time series discord discovery algorithm [3]

[1] Lin, J., Keogh, E., Patel, P., and Lonardi, S., 
[*Finding Motifs in Time Series*](http://cs.gmu.edu/~jessica/Lin_motif.pdf), 
The 2nd Workshop on Temporal Data Mining, the 8th ACM Int'l Conference on KDD (2002)

[2] Patel, P., Keogh, E., Lin, J., Lonardi, S., 
[*Mining Motifs in Massive Time Series Databases*](http://www.cs.gmu.edu/~jessica/publications/motif_icdm02.pdf), 
In Proc. ICDM (2002)

[3] Keogh, E., Lin, J., Fu, A.,
[*HOT SAX: Efficiently finding the most unusual time series subsequence*](http://www.cs.ucr.edu/~eamonn/HOT%20SAX%20%20long-ver.pdf),
In Proc. ICDM (2005)

##### _Note that curretly I am working on the similar in functionality [R code](https://github.com/jMotif/jmotif-R)_

#### Citing this work:

If you are using this implementation for you academic work, please cite our [Grammarviz 2.0 paper](http://link.springer.com/chapter/10.1007/978-3-662-44845-8_37):

[[Citation]](https://raw.githubusercontent.com/jMotif/SAX/master/citation.bib) Senin, P., Lin, J., Wang, X., Oates, T., Gandhi, S., Boedihardjo, A.P., Chen, C., Frankenstein, S., Lerner, M.,  [*GrammarViz 2.0: a tool for grammar-based pattern discovery in time series*](http://www2.hawaii.edu/~senin/assets/papers/grammarviz2.pdf), ECML/PKDD Conference, 2014.

#### an alternative solution for recurrent and anomalous patterns detection:
If you are interested in more advance techniques for time series pattern discovery -- the one which allows to discover recurrent and anomalous patterns of variable length -- please check out our new tool called [GrammarViz 2.0](http://grammarviz2.github.io/grammarviz2_site/index.html). Based on symbolic discretization, Grammatical Inference, and algorithmic (i.e., Kolmogorv complexity) this new approach facilitates linear-time variable length motifs discovery and orders of magnitude faster than HOT-SAX discords discovery (but exactness is not guaranteed).

0.0 SAX transform in a nutshell
------------
SAX is used to transform a sequence of rational numbers (i.e., a time series) into a sequence of letters (i.e., a string). An illustration of a time series of 128 points converted into the word of 8 letters:

![SAX in a nutshell](https://raw.githubusercontent.com/jMotif/SAX/master/src/resources/sax.png)

As the discretization is the probably the most used transformation in data mining, SAX has been widely used throughout the field. Find more information about SAX at its authors pages: [SAX overview by Jessica Lin](http://cs.gmu.edu/~jessica/sax.htm), [Eamonn Keogh's SAX page](http://www.cs.ucr.edu/~eamonn/SAX.htm), or at [sax-vsm wiki page](http://jmotif.github.io/sax-vsm_site/morea/algorithm/SAX.html).

1.0 Building
------------
The code is written in Java and I use maven to build it. Use the profile `single` to build an executable jar containing all the dependencies:
	
	$ mvn package -P single
	
	[INFO] Scanning for projects...
	[INFO] ------------------------------------------------------------------------
	[INFO] Building jmotif-sax
	[INFO]    task-segment: [package]
	
	...
	
	[INFO] Building jar: /media/Stock/git/jmotif-sax/target/jmotif-sax-1.0.1-SNAPSHOT-jar-with-dependencies.jar
	[INFO] ------------------------------------------------------------------------
	[INFO] BUILD SUCCESSFUL

2.0 Time series to SAX conversion using CLI
------------
The jar file can be used to convert a time series (represented as a single-column text file) to SAX via sliding window in command line:

	$ java -jar target/jmotif-sax-0.1.1-SNAPSHOT-jar-with-dependencies.jar
	Usage: <main class> [options] 
  	Options:
    		--alphabet_size, -a
    		   SAX alphabet size, Default: 3
    		--data, -d
    		   The input file name
    		--out, -o
       		   The output file name
    		--strategy
       		   SAX numerosity reduction strategy
       		   Default: EXACT, Possible Values: [NONE, EXACT, MINDIST]
    		--threads, -t
       		   number of threads to use, Default: 1
    		--threshold
       		   SAX normalization threshold, Default: 0.01
    		--window_size, -w
       		   SAX sliding window size, Default: 30
    		--word_size, -p
       		   SAX PAA word size, Default: 4

When run, it prints the time series point index and a corresponding word:

 	$ java -jar "target/jmotif-sax-1.0.1-SNAPSHOT-jar-with-dependencies.jar" \ 
 	                      -d src/resources/test-data/ecg0606_1.csv -o test.txt
 	$ head test.txt
 	0, aabc
	8, aacc
	13, abcc
	20, abcb
	...

3.0 API usage
------------	
There two classes implementing end-to-end workflow for SAX. These are [TSProcessor](https://github.com/jMotif/SAX/blob/master/src/main/java/net/seninp/jmotif/sax/TSProcessor.java) (implements time series-related functions) and [SAXProcessor](https://github.com/jMotif/SAX/blob/master/src/main/java/net/seninp/jmotif/sax/SAXProcessor.java) (implements the discretization). Below are typical use scenarios:

#### 3.1 Discretizing time-series *by chunking*:

	// instantiate classes
	NormalAlphabet na = new NormalAlphabet();
	SAXProcessor sp = new SAXProcessor();
	
	// read the input file
	double[] ts = TSProcessor.readFileColumn(dataFName, 0, 0);
	
	// perform the discretization
	String str = sp.ts2saxByChunking(ts, paaSize, na.getCuts(alphabetSize), nThreshold);

	// print the output
	System.out.println(str);

#### 3.2 Discretizing time-series *via sliding window*:

	// instantiate classes
	NormalAlphabet na = new NormalAlphabet();
	SAXProcessor sp = new SAXProcessor();
	
	// read the input file
	double[] ts = TSProcessor.readFileColumn(dataFName, 0, 0);
	
	// perform the discretization
	SAXRecords res = sp.ts2saxViaWindow(ts, slidingWindowSize, paaSize, 
		na.getCuts(alphabetSize), nrStrategy, nThreshold);

	// print the output
	Set<Integer> index = res.getIndexes();
	for (Integer idx : index) {
		System.out.println(idx + ", " + String.valueOf(res.getByIndex(idx).getPayload()));
	}

#### 3.3 Multi-threaded discretization *via sliding window*:

	// instantiate classes
	NormalAlphabet na = new NormalAlphabet();
	SAXProcessor sp = new SAXProcessor();
  
	// read the input file
	double[] ts = TSProcessor.readFileColumn(dataFName, 0, 0);

	// perform the discretization using 8 threads
	ParallelSAXImplementation ps = new ParallelSAXImplementation();
	SAXRecords res = ps.process(ts, 8, slidingWindowSize, paaSize, alphabetSize, 
		nrStrategy, nThreshold);

	// print the output
	Set<Integer> index = res.getIndexes();
	for (Integer idx : index) {
		System.out.println(idx + ", " + String.valueOf(res.getByIndex(idx).getPayload()));
	}

#### 3.4 Time series motif (recurrent pattern) discovery
Class [SAXRecords](https://github.com/jMotif/SAX/blob/master/src/main/java/net/seninp/jmotif/sax/datastructure/SAXRecords.java) implements a method for getting the most frequent SAX words:

        // read the data
	double[] series = TSProcessor.readFileColumn(DATA_FNAME, 0, 0);
	
	// instantiate classes
	Alphabet na = new NormalAlphabet();
	SAXProcessor sp = new SAXProcessor();
	
	// perform discretization
	saxData = sp.ts2saxViaWindow(series, WIN_SIZE, PAA_SIZE, na.getCuts(ALPHABET_SIZE),
        		NR_STRATEGY, NORM_THRESHOLD);
        		
        // get the list of 10 most frequent SAX words
	ArrayList<SAXRecord> motifs = saxData.getMotifs(10);
	SAXRecord topMotif = motifs.get(0);
        
        // print motifs
	System.out.println("top motif " + String.valueOf(topMotif.getPayload()) + " seen " + 
    	   		topMotif.getIndexes().size() + " times.");

#### 3.5 Time series discord discovery using brute-force search

The [BruteForceDiscordImplementation](https://github.com/jMotif/SAX/blob/master/src/main/java/net/seninp/jmotif/sax/discord/BruteForceDiscordImplementation.java) class implements a brute-force search for discords, which is intended to be used as a reference in tests (HOTSAX and NONE yield exactly the same discords).

 	discordsBruteForce = BruteForceDiscordImplementation.series2BruteForceDiscords(series, 
 	   WIN_SIZE, DISCORDS_TO_TEST, new LargeWindowAlgorithm());
        
        for (DiscordRecord d : discordsBruteForce) {
           System.out.println("brute force discord " + d.toString());
        }

#### 3.6 Time series discord detection using HOTSAX

The [HOTSAXImplementation](https://github.com/jMotif/SAX/blob/master/src/main/java/net/seninp/jmotif/sax/discord/HOTSAXImplementation.java) class implements a HOTSAX algorithm for time series discord discovery:

      discordsHOTSAX = HOTSAXImplementation.series2Discords(series, DISCORDS_TO_TEST, WIN_SIZE,
          PAA_SIZE, ALPHABET_SIZE, STRATEGY, NORM_THRESHOLD);
          
      for (DiscordRecord d : discordsHOTSAX) {
        System.out.println("hotsax hash discord " + d.toString());
      }

Note, that the "proper" strategy to use with HOTSAX is `NumerosityReductionStrategy.NONE` but you may try others in order to speed-up the search, exactness however, is not guaranteed.

The library source code has examples (tests) for using these [here](https://github.com/jMotif/SAX/blob/master/src/test/java/net/seninp/jmotif/sax/discord/TestDiscordDiscoveryNONE.java) and [here](https://github.com/jMotif/SAX/blob/master/src/test/java/net/seninp/jmotif/sax/discord/TestDiscordDiscoveryEXACT.java).
	

4.0 Threaded performance
------------	
The plot shows the speedup achieved when using the parallelized SAX version on the dataset [`300_signal1.txt`](https://raw.githubusercontent.com/jMotif/SAX/master/src/resources/test-data/300_signal1.txt) of length 536,976 points. Parameters used in the experiment: sliding window size 200, PAA size 11, alphabet size 7, and three different NR strategies.

Note, that for MINDIST numerosity reduction strategy the parallelized code performs NONE-based discretization first and prunes the result second. The difference in performance for 7+ CPUs on the plot below is due to the uneven server load, I guess.

![Performance plot](https://raw.githubusercontent.com/jMotif/SAX/master/src/RCode/performance/profiling.png)

## Made with Aloha!
![Made with Aloha!](https://raw.githubusercontent.com/GrammarViz2/grammarviz2_src/master/src/resources/assets/aloha.jpg)

#### Versions:
`1.0.5 - 1.0.6`
  * introduced the discretization approximation error

`1.0.4`
  * fixed the SAX transform via sliding window, last window now added to the resulting pool

`1.0.3`
  * improved PAA performance for even length/paa case, now it is about 3 times faster

`0.0.1 - 1.0.2`
  * additional testing and code test coverage control

`0.0.1 - 1.0.0`
  * cleaning up the old JMotif code and decoupling the SAX code from the rest

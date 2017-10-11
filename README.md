# Using the Apache SystemML API on a Spark Shell with IBM Analytics Engine

## SystemML on Spark Shell using the IBM Analytics Engine (IAE)? Yes!

## A very simple way of using SystemML for all of your machine learning and big data needs. This tutorial will get you set up and running SystemML on the Spark Shell using IAE like a star.

## Not familiar with Apache SystemML?

### At a high-level, SystemML is what is used for the machine learning and mathematical part of your data science project. You can log into Spark Shell, load SystemML on the shell, load your data and write your linear algebra, statistical equations, matrices, etc. in code much shorter than it would be in the Spark shell syntax. It helps not only with mathematical exploration and machine learning algorithms, but it also allows you to be on Spark where you can do all of the above with really big data that you couldn't use on your local computer. 

## Not familiar with how to set up an Apache Spark cluster?

### By using the IBM Analytics Engine you can spin up a Spark cluster in just a few minutes using the web user interface.

### With both of these tools, I'll walk you through how to set up your computer for all of SystemML's assumptions, how to set up IAE and your Spark cluster, SSH in to connect to your Spark cluster on your computer and load Spark Shell with SystemML,then load some data and do a few examples in scala. Whew that's a lot, but we I promise I go through it all!

## Now let's get going on our learning. First step: assumptions for SystemML.

### Have Java, Scala, wget and Spark installed on your computer. (One by one, copy and paste each line into your terminal and push enter.)

brew tap caskroom/cask  
brew install Caskroom/cask/java  
brew install scala  
brew install wget  
brew install apache-spark  


## Now let's set up IAE! Go to https://developer.ibm.com/clouddataservices/docs/ibm-analytics-engine/
(This assumes you have an account, so if you do not, go ahead and set one up and come back to this step.)

### Select "IBM Analytics Engine service on Bluemix" beneath the video (which is super handy if you want to watch it!)
### Now choose which selections you want (you can leave everything at its default for the purpose of this tutorial) and push "Create" at the bottom of the page. This may take a few minutes.

### Once your cluster has been created, make sure you are in the "Manage" section. If you are not, navigate to it! In this section you'll notice there is a lot of information. The areas you want to focus on are "Launch Console", username, password and SSH.

### Go ahead and copy your username and password and push "Launch Console" to log you into Ambari.

### Once that's complete go back to your "Manage" console and copy your SSH line.

### Go to your terminal and paste the SSH line in it and press enter. You will be prompted for a password. Use your Ambari password.

## Logged in? You're a rockstar! Now we can start SystemML!

### First download SystemML (we are still at your terminal)

wget https://sparktc.ibmcloud.com/repo/latest/SystemML.jar  

### Now type the following code to access the Spark Shell with SystemML.

spark-shell --executor-memory 4G --driver-memory 4G --jars SystemML.jar  

### Now, using the Spark Shell (Scala), import the MLContext to start SystemML.

import org.apache.sysml.api.mlcontext._  
import org.apache.sysml.api.mlcontext.ScriptFactory._  
val ml = new MLContext(sc)  

## Congratulations!! NOW YOU ARE IN APACHE SYSTEMML!! Look at you.

*In the future you will just need to do the last two steps to get this going and you can also repeat these last steps on a *local Spark Shell.

## Let's now figure out how to load a script and run it as well as load data and run some examples so that you can get familiar with Spark Shell and SystemML.

## Here's a quick example: Script from a URL.
Here s1 is created by reading Univar-Stats.dml from a URL address.

val uniUrl = "https://raw.githubusercontent.com/apache/incubator-systemml/master/scripts/algorithms/Univar-Stats.dml"  
val s1 = ScriptFactory.dmlFromUrl(uniUrl)  

### Our next step is to parallelize the information, read in two matrices as RDDs, getting the sum of the first, the sum of the second and a message.

scala> val data1 = sc.parallelize(Array("1.0,2.0", "3.0,4.0”))  
scala> val data2 = sc.parallelize(Array("5.0,6.0", "7.0,8.0”))  
scala>val s = """  
     | s1 = sum(m1);
     | s2 = sum(m2);
     | if (s1 > s2) {
     |  message = "s1 is greater"
     | } else if (s2 > s1) {
     |  message = "s2 is greater"
     | } else {
     |  message = "s1 and s2 are equal"
     | }
     | """

scala> val script = dml(s).in("m1",data1).in("m2", data2).out("s1","s2", "message”)  

### Your should get:

script: org.apache.sysml.api.mlcontext.Script =  
Inputs:  
[1] (RDD) m1: ParallelCollectionRDD[0] at parallelize at <console>:33
[2] (RDD) m2: ParallelCollectionRDD[1] at parallelize at <console>:33

Outputs:  
[1] s1
[2] s2
[3] message

### Now print your script info. You should see:

scala> println(script.info)  
Script Type: DML

Inputs:  
[1] (RDD) m1: ParallelCollectionRDD[0] at parallelize at <console>:33
[2] (RDD) m2: ParallelCollectionRDD[1] at parallelize at <console>:33

Outputs:  
[1] s1
[2] s2
[3] message

Input Parameters:  
None

Input Variables:  
[1] m1
[2] m2

Output Variables:  
[1] s1
[2] s2
[3] message

Symbol Table:  
[1] (Matrix) m1: Matrix: null, [-1 x -1, nnz=-1, blocks (1 x 1)], csv, not-dirty
[2] (Matrix) m2: Matrix: null, [-1 x -1, nnz=-1, blocks (1 x 1)], csv, not-dirty

Script String:

s1 = sum(m1);  
s2 = sum(m2);  
if (s1 > s2) {  
 message = "s1 is greater"
} else if (s2 > s1) {
 message = "s2 is greater"
} else {
 message = "s1 and s2 are equal"
}

Script Execution String:  
m1 = read('');  
m2 = read('');

s1 = sum(m1);  
s2 = sum(m2);  
if (s1 > s2) {  
 message = "s1 is greater"
} else if (s2 > s1) {
 message = "s2 is greater"
} else {
 message = "s1 and s2 are equal"
}
write(s1, '');  
write(s2, '');  
write(message, '');  

### Execute your script and get your results!

scala> val results = ml.execute(script)  
results: org.apache.sysml.api.mlcontext.MLResults =  
[1] (Double) s1: 10.0
[2] (Double) s2: 26.0
[3] (String) message: s2 is greater

### Just as an example, you can set your value as x and get your results in Double form.

scala> val x = results.getDouble("s1")  
x: Double = 10.0

scala> val y = results.getDouble("s2")  
y: Double = 26.0

scala> x + y  
res1: Double = 36.0  

### Here is another version of the example. Because the API is very Scala friendly, you can pull out your results as a Scala tuple.

scala> val (firstSum, secondSum, sumMessage) = results.getTuple[Double, Double, String]("s1", "s2", "message")  
firstSum: Double = 10.0  
secondSum: Double = 26.0  
sumMessage: String = s2 is greater  

### Here is another really handy part. As another example you can load in your data, type the short code and get a whole table of standard statistical measures for each feature!

### To do this, let's first get our data into Spark.
*We first want to make sure our data is clean and ready to go. Let's load in some data and run a SystemML script.

scala> val habermanUrl = "http://archive.ics.uci.edu/ml/machine-learning-databases/haberman/haberman.data"

scala> val habermanList = scala.io.Source.fromURL(habermanUrl).mkString.split("\n")

scala> val habermanRDD = sc.parallelize(habermanList)

scala> val typesRDD = sc.parallelize(Array("1.0,1.0,1.0,2.0"))

scala> val scriptUrl = "https://raw.githubusercontent.com/apache/incubator-systemml/master/scripts/algorithms/Univar-Stats.dml"

scala> val script = dmlFromUrl(scriptUrl).in("A", habermanRDD, habermanMetadata).in("K", typesRDD, typesMetadata).in("$CONSOLE_OUTPUT", true)  

scala> val results = ml.execute(script)

Feature [1]: Scale  
 (01) Minimum             | 30.0
 (02) Maximum             | 83.0
 (03) Range               | 53.0
 (04) Mean                | 52.45751633986928
 (05) Variance            | 116.71458266366658
 (06) Std deviation       | 10.803452349303281
 (07) Std err of mean     | 0.6175922641866753
 (08) Coeff of variation  | 0.20594669940735139
 (09) Skewness            | 0.1450718616532357
 (10) Kurtosis            | -0.6150152487211726
 (11) Std err of skewness | 0.13934809593495995
 (12) Std err of kurtosis | 0.277810485320835
 (13) Median              | 52.0
 (14) Interquartile mean  | 52.16013071895425
-------------------------------------------------
Feature [2]: Scale  
 (01) Minimum             | 58.0
 (02) Maximum             | 69.0
 (03) Range               | 11.0
 (04) Mean                | 62.85294117647059
 (05) Variance            | 10.558630665380907
 (06) Std deviation       | 3.2494046632238507
 (07) Std err of mean     | 0.18575610076612029
 (08) Coeff of variation  | 0.051698529971741194
 (09) Skewness            | 0.07798443581479181
 (10) Kurtosis            | -1.1324380182967442
 (11) Std err of skewness | 0.13934809593495995
 (12) Std err of kurtosis | 0.277810485320835
 (13) Median              | 63.0
 (14) Interquartile mean  | 62.80392156862745
-------------------------------------------------
Feature [3]: Scale  
 (01) Minimum             | 0.0
 (02) Maximum             | 52.0
 (03) Range               | 52.0
 (04) Mean                | 4.026143790849673
 (05) Variance            | 51.691117539912135
 (06) Std deviation       | 7.189653506248555
 (07) Std err of mean     | 0.41100513466216837
 (08) Coeff of variation  | 1.7857418611299172
 (09) Skewness            | 2.954633471088322
 (10) Kurtosis            | 11.425776549251449
 (11) Std err of skewness | 0.13934809593495995
 (12) Std err of kurtosis | 0.277810485320835
 (13) Median              | 1.0
 (14) Interquartile mean  | 1.2483660130718954
-------------------------------------------------
Feature [4]: Categorical (Nominal)  
 (15) Num of categories   | 2
 (16) Mode                | 1
 (17) Num of modes        | 1
results: org.apache.sysml.api.mlcontext.MLResults =  
[1] (Matrix) baseStats: Matrix: scratch_space/_p5250_9.31.116.229/parfor/2_resultmerge1, [17 x 4, nnz=44, blocks (1000 x 1000)], binaryblock, dirty

### You can also ask for the base stats.

scala> val baseStats = results.getMatrix("baseStats")  
baseStats: org.apache.sysml.api.mlcontext.Matrix = org.apache.sysml.api.mlcontext.Matrix@237cd4e5

scala> baseStats.  
asDataFrame          asDoubleMatrix       asInstanceOf         asJavaRDDStringCSV   asJavaRDDStringIJV   asMLMatrix           asMatrixObject       asRDDStringCSV  
asRDDStringIJV       isInstanceOf         toString             

### You can also get the base stats as an RDD. Note: IJV leaves out non values and CSV includes them. Here's an example of both:

scala> baseStats.asRDDString  
asRDDStringCSV   asRDDStringIJV   

scala> baseStats.asRDDStringCSV.collect  
res4: Array[String] = Array(30.0,58.0,0.0,0.0, 83.0,69.0,52.0,0.0, 53.0,11.0,52....1.0)

scala> baseStats.asRDDStringIJV.collect  
res5: Array[String] = Array(1 1 30.0, 1 2 58.0, 1 3 0.0, 1 4 0.0, 2 1 83.0, 2 2 69.0, 2 3 52.0, 2 4 0.0, ... 1...  

## I think that's a great start to using SystemML with Spark Shell on the IBM Analytics Engine! Once you're done you can quit to exit.

:quit

# You have successfully set up your computer for running SystemML and Spark on the IBM Analytics Engine (IAE), loaded the Spark shell, ran scripts, loaded data and run some examples!! Congrats!! Now go save the world with these awesome new skills.

 Copyright 2017 IBM Corp. All Rights Reserved.

  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.

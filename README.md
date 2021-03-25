# MP1---NSM170002
## Navin Mathew
## SE 4367.001

This is a github containing the files and README file for MP1 for SE 4367.001 by Navin Mathew (NSM170002)

## Part 0: Getting Started 

I was able to successfully set up the Java JRE library and Soot library to my Eclipse workspace. The demonstration of the setup on Microsoft Teams was very useful to follow through the initial struggles of setting up the path for the Java JRE library. Eventually, I was able to set up a new Java project called a, where I am able to keep my java files in. In order to test if Soot was working properly, I ran the TestSoot.java and HelloThread.java provided. It compiled without any errors. 

## Part 1: Control Flow Analysis - Finding Dominators

### Your task: given a method m and a statement s, to find all the statements in m that dominate s. We will use the following algorithm where D(n) denotes a set of dominators of a node n:The class DominatorFinder has been partially implemented. You will need to complete the method doAnalysis. You may also add new fields and methods

I found that the doAnalysis section already contained the correct code. In order to prove the code worked I created a test case to evaluate if the algorithm found the correct dominators.

The test case is located in TestCase.java and has the following code inside the main.

int x  = 5;
while(x>0) {
	x--;
}
System.out.println(x);


It is apparent that (int x = 5) is dominated by nothing other than itself inside the main. While the while block is dominated by (int x = 5). Likewise, (x--) is dominated by (x = 5), while(x>0), and itself. (System.out.println(x)) is dominated by (x = 5), (while(x>0), (x--), and itself.

Using the doAnalysis given and running the TestDominatorFinder java code, it becomes apparent that the expected dominators for each statement is correct. Although the TestDominatorFinder also analyzes the package name and the main class itself for dominators, the inside statements of the main class, as shown above, are correct in the way it has been outputted by its dominators. 

Using this test case, it becomes more apparent that the doAnalysis given runs correctly as intended.

## Part 2: Data Flow Analysis - Call Graph Construction

### Your task: run TestSootCallGraph on Example (add both TestSootCallGraph.java and Example.java to your project source folder) to compare the precision and speed between CHA and PTA

Using the CHA and PTA methods already initialized in TestSootCallGraph, I ran both of them and the outputs are shown below.

CHA

![screencapture1](https://user-images.githubusercontent.com/76182733/112407743-a4306800-8ce4-11eb-88be-e9dc7ead7e1d.JPG)


PTA

![screencapture2](https://user-images.githubusercontent.com/76182733/112407772-b0b4c080-8ce4-11eb-9387-e82fd4259ba2.JPG)


From the figure above, it becomes apparent that the PTA method is more precise than the CHA method as it was able to find that animal.saySomething() calls Cat.saySomething() in 7 edges, while the CHA method took 12 edges. Additionally, the PTA is faster in terms of speed to the CHA method as I took a timestamp of the start of call and end of the call in nanoseconds. The length the method ran is outputted on the image above. The CHA method took 206183100 nanoseconds, while the PTA method was able to compile in 214300 nanoseconds. This ultimately shows that the PTA method is more efficient than the CHA method. 

## Part 3: Program Instrumentation with Soot
### Your task: 
### Understand the code in TestSootLogging.java and run it. It will generate a file Example.jimple in a folder "sootOutput" under your project directory. Read and understand the Jimple code in Example.jimple. 
 ### In the main method of TestSootLogging, comment the statement Options.v().set_output_format(1); and run again. It will generate a Java class file Example.class in "sootOutput". Run Example to see the output.

I was able to run the TestSootLogging.java and compile it by running Example.java within it. This resulted in a new folder called sootOutput inside the project library a1. After checking the library, I was able to see 4 jimple files which were a1.Animal.jimple, a1.Cat.jimple, a1.Example.jimple, and a1.Fish.jimple. This led me to understand the use of the Option.v().set_output_format(1) as it was able to create a jimple file. I then scanned the Jimple and understood the java code was converted to jimple which is a easier way to understand statements. I compared it to the Example.java and was able to determine that the context of the statements were all exactly the same.

I then commented out the Option.v().set_output_format(1) and found that it generated .class files instead of jimple. After discovering this I ran the output as shown below.

![screencapture3](https://user-images.githubusercontent.com/76182733/112407808-bad6bf00-8ce4-11eb-856b-6d91d5797ea2.JPG)



This demonstrates how the TestSootLogging.java was able to find what is referencing what and ultimately answer the question which method does animal.saySomething() in the main
method call. 

### Your task: write code to instrument every field access in HelloThread to print out the access information. For example, a typical execution of the instrumented HelloThread will print: (code shown in MP1 instructions)

This task took me a lot of time to figure out. I spent multiple days testing which methods of stmt can find the parameters for Log.logFieldAcc within the “if stmt.containsFieldRef()” statement. I tried out the different fields and found that stmt has a getFieldRef().getField().isStatic() method. This allowed me to know I can check if a statement is static or not. I then found that I can get the name of the thread and variable name through the stmt.getFieldRef().getField().getSignature().toString() method. I then needed to find a way to know the boolean if write is used or not. After looking at the values of stmt, I was able analyze that the stmt.getDefBoxes().toString().contains("($") being true meant that the write status was false as there was only three instance of writing according to the sample output. This meant that the presence of “($” was a factor in determining if the write status is true or false. I was able to then find the object o that I am to pass by calling the current thread.  I knew I needed to pass a thread, but did not know what method can be passed. Through some research, I learned that the Thread.currentThread() basically configured the current thread that was running. In overall, I was able to successfully implement the Log.LogFieldAcc method to print out the statements of the heap. Although the other is little different from the sample, it ultimately traces the heap. 

The output is the following:

#### Thread Thread-9 wrote static field <a1.HelloThread: int x>

#### Thread Thread-10 wrote static field <a1.HelloThread: int x>

#### Thread Thread-10 read instance field <a1.HelloThread$TestThread: int y> of object Thread[Thread-10,5,Soot Threadgroup]

#### Thread Thread-10 wrote instance field <a1.HelloThread$TestThread: int y> of object Thread[Thread-10,5,Soot Threadgroup]

#### Thread Thread-9 read instance field <a1.HelloThread$TestThread: int y> of object Thread[Thread-9,5,Soot Threadgroup]

#### Thread Thread-9 read static field <a1.HelloThread: int x>

#### Thread Thread-9 read static field <java.lang.System: java.io.PrintStream out> 



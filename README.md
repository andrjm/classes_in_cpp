# Classes in C++

This repository contains a basic introduction to classes in C++. We use the trapezoidal rule for integration as the solution to a basic numerical integration problem.
We develop a class called Trapezoidal that serve as the integration solver. It is aimed at people new to C++ that studies computational science or similar subjects.

### Direct links to the actual codes:
  - [trapezoidal.hpp](https://github.com/reneaas/classes_in_cpp/blob/master/trapezoidal.hpp)
  - [trapezoidal.cpp](https://github.com/reneaas/classes_in_cpp/blob/master/trapezoidal.cpp)
  - [makefile](https://github.com/reneaas/classes_in_cpp/blob/master/makefile)
  - [main.cpp](https://github.com/reneaas/classes_in_cpp/blob/master/main.cpp)

### Basic ingredients of classes in C++

There are 4 basic ingredients we need when we use classes in C++:

1. A main code, usually called main.cpp.
    * The main program is where you use the actual class.
    * In our case, we use it to solve a given integral.

2. A class code, usually named class_name.cpp
    * In our case, it would be called trapezoidal.cpp.
    * This is where all functions are specified.

3. A header file, usually names class_name.hpp
    * This is where we declare the class, its variables and functions/methods.

4. A makefile
    * A makefile is usually just called a makefile or Makefile.
    * This is where you instruct the compiler to compile and execute the code.


At this point, you may wonder: where do we start? The answer is, the header file.

### The header file
We begin our project by thinking about what type of functions we need. In our problem we need the following:

1. A function that initializes the solver
  - This usually means that we specify certain parameters of the class.
  - For the trapezoidal rule this means to specify the integration limits and how many integration points we want.
  - In some cases, this could mean a function that reads data from a file and initializes parameters in the class.

2. A function that solves the problem
  - This is not necessarily always a single function though.
  - In our case, it suffices to have a single function that implements the trapezoidal rule for integration, since this algorithm is pretty simple.

3. A function that writes to file or provides output.
  - In our case, we will just print the computed value to the terminal.
  - In many cases though, it's necessary to write computed values to a file, but we'll restrict ourselves to simply printing the result.


#### Structure of the header file

The basic structure of the header file should be something like this:

```c++
#ifndef HEADERFILENAME_HPP
#define HEADEFILERNAME_HPP

class ClassName {
private:
  /* class variables */

public:
  /* function declarations */
};
#endif
```

In our case, this basic formula translates to the following header file (recall that we called the header file trapezoidal.hpp):

```c++
#ifndef TRAPEZOIDAL_HPP
#define TRAPEZOIDAL_HPP

class Trapezoidal {
private:
  double m_a, m_b;      //Integration interval [a,b]
  int m_N;      //Number of integration points.
  double m_stepsize;      //Stepsize in the integration algorithm.
  double m_I = 0.;     //Will store the value of the integral.


public:
  void Initialize(double a, double b, int N);     //Initialize the parameters m_a, m_b,
  void Integrate(double f(double x));
  void Print();
};

#endif
```

Let's discuss a few details here:
- We have to sections: *private* and *public*.
  1. In the *private* section we declare variables that we need inside the class but that we do not need to access directly from outside the class. (I typically declare all my variables private and use class functions to print, write to file and so forth so I never need to manually change anything)
  2. The *public* section is where we declare the class functions.
    - You may declare variables here too if you want to access or change them from the outside of the class, but we'll restrict ourselves to the simpler case where no variable is public.
- I've used "m_" in all the class variables. This is to distinguish them from the variables we send in. As a result, we get a better overview of which variables belong to the class (called member variables), and which variables do not.
  - You can think of "m_" as the analogue to "self." in python, but it's not something you *must* write in C++.
  - It's just a convention so that when other people read your code, they can more easily understand it.
- All the functions are defined as type *void*. This is because we don't really need access to the variables in any way since we're going to create a class function that prints the result to the screen. After all, all functions inside the class has access to the variables in under the "private" section.


### The class code

Now that we've declared the class as well as all its variables and functions, we may write the specific class code. The result may be as below:

```c++
#include "trapezoidal.hpp"
#include <iostream>     //Provides us with function to print to terminal
#include <cmath>        //Provides us with mathematical functions such as sin, cos, exp and so on.

using namespace std;

void Trapezoidal::Initialize(double a, double b, int N)
{
  m_a = a;      //Start point of integration
  m_b = b;      //End point of integration
  m_N = N;      //Number of integration points
  m_stepsize = (b-a)*(1./N);
}

void Trapezoidal::Integrate(double f(double x)){
  double x = 0; //This variable is only needed in this function, so not a member variable.
  //From here we just implement the trapezoidal rule for integration.
  m_I += 0.5*(f(m_a) + f(m_b));
  for (int i = 0; i < m_N; i++){
    x = m_a + i*m_stepsize;
    m_I += f(x);
  }
  m_I *= m_stepsize;
}

void Trapezoidal::Print(){
  //This is an example on how to print to terminal in C++.
  cout << "Integral = " << m_I << endl;
}
```

Let's look at some details here:

1. We must include the header file at the top. We use #include" " instead of #include< > for header files.
2. When we specify the function, we must write:
  - Type ClassName::FunctionName(arguments)

### The main code

Now that we have our header file and class code, we can proceed to use it. This is done via the so-called main code or main program. The general structure of such a code is:

```c++

#include<library1>
#include<library2>

using namespace std; //You can generally always write this namespace in.

int main(int argc, char const *argv[]) {
  /* Write your code here */
  return 0;
}
```

You do not need to worry to much about that the arguments to the main function is for our purpose. In our case, the realization is

```c++
#include "trapesmetoden.hpp"
#include <iostream>     //Need it to print stuff
#include <cmath>        //Need math functions

using namespace std;

double f(double x); //We declare the function here, but specify it at the bottom.

int main(int argc, char const *argv[]) {
  //Parameters
  double a = 0;   //Start point of integration
  double b = 1;   //End point of integration
  int N = 1000;   //Number of integration points

  //Solve integral
  Trapezoidal my_solver;    //Create the solver of type Trapezoidal
  my_solver.Initialize(a, b, N);  //Initialize the parameters in the class/solver
  my_solver.Integrate(f); //Solve the integral
  my_solver.Print();    //Print it to terminal

  return 0;   //Don't worry about this one, but you should always let it stay here at the bottom of the main function.
}

//The function we want to integrate
double f(double x){
  return exp(-x);
}
```


### Makefile
At this point we're done with the codes, but how do we run them? Let's tackle this.

In this case, the makefile need only looks as follows

```makefile
all: compile execute

compile:
	c++ -o main.out main.cpp trapezoidal.cpp

execute:
	./main.out
```

Let's discuss this in some detail:
1. The top line tells the computer that if we write the following the terminal,
  ```terminal
  make all
  ```
  it should do both the "compile" and "execute" part.

2. The "compile" part is essentially how to compile the code without any fancy optimizations and stuff. It compiles all the .cpp codes and places the runable or *executable* program in "main.out".
3. The "execute" part tells the computer to run the executeable file "main.out".
  - the dot "." in "./main.exe" tells the computer that the file in is the current directory.
  - In other words, "./main.exe" is the full so-called *path* to the executable file.

For the codes written in this small tutorial, we get the following output when we write "make all":

```terminal
make all
c++ -o main.out main.cpp trapezoidal.cpp
./main.out
Integral = 0.633121
```
- The first line is what we write to compile and run the code.
- The second and third line is just the computer telling us precisely which instructions it performs.
- The fourth line is the output from the executable file, which in our case is an approximation to the integral.


#### That's it! A small hands-on tutorial on how to write a class in C++.

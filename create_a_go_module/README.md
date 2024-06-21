- This tutorial's sequence includes seven brief topics that each illustrate a different part of the language.
	- **Create a module** -- Write a small module with functions you can call from another module.
	  logseq.order-list-type:: number
	- **Call your code from another module** -- Import and use your new module.
	  logseq.order-list-type:: number
	- **Return and handle an error** -- Add simple error handling.
	  logseq.order-list-type:: number
	- **Return a random greeting** -- Handle data in slices (Go's dynamically-sized arrays).
	  logseq.order-list-type:: number
	- **Return greetings for multiple people** -- Store key/value pairs in a map.
	  logseq.order-list-type:: number
	- **Add a test** -- Use Go's built-in unit testing features top test your code.
	  logseq.order-list-type:: number
	- **Compile and install the application** -- Compile and install your code locally.
	  logseq.order-list-type:: number
- ---
- Go code is grouped into packages, and packages are grouped into modules. Your module specifies dependencies needed to run your code, including the Go version and the set of other modules it requires.
  
  As you add or improve functionality in your module, you publish new versions of the module. Developers writing code that calls functions in your code module can import the module's updated packages and test with the new version before putting it into production use.
- ---
- ## Create a Module
  id:: 6674e90c-3c34-4a34-8c7e-5aad20d99fc5
- Create a *greetings* directory for your Go module source code.
  logseq.order-list-type:: number
- Start your module using the `go mod init command`.
  logseq.order-list-type:: number
	- The `go mod init` command creates a go.mod file to track your code's dependencies. So far, the file includes only the name of your module and the Go version your code supports. But as you add dependencies, the go.mod file will list the versions your code depends on. This keeps builds reproducible and gives you direct control over which module versions to use.
	  logseq.order-list-type:: number
- Create a file and call it `greetings.go`.
  logseq.order-list-type:: number
- Write the following code into your `greetings.go` file and save the file.
  logseq.order-list-type:: number
	- logseq.order-list-type:: number
	  ```go
	  package greetings
	  
	  import "fmt"
	  
	  func Hello(name string) string {
	      message := fmt.Sprintf("Hi, %v. Welcome!", name)
	      return message
	  }
	  ```
	- This is the first code for your module. It returns a greeting to any caller that asks for one.
	  logseq.order-list-type:: number
	- You can see the [[Structure of a Go function]] .
	  logseq.order-list-type:: number
- In Go, the `:=` operator is a shortcut for declaring and initializing a variable in one line (Go uses the value on the right to determine the variable's type). Taking the long way, you might have written this as:
	- ```go
	  var message string
	  message = fmt.Sprintf("Hi, %v. Welcome!", name)
	  ```
- ---
- ## Call your code from another module
  In this section, you'll write code to make calls to the `Hello` function in the module you just wrote.
-
- Create a `hello` directory for your Go module source code.
  logseq.order-list-type:: number
  ```
  <home>/
  |-- greetings/
  |-- hello/
  ```
- Enable dependency tracking for the code you're about to write.
  logseq.order-list-type:: number
  ```
  $ go mod init example.com/hello
  go: creating a new go.mod: module example.com/hello
  ```
- Create a file in which to write your code and call it `hello.go`.
  logseq.order-list-type:: number
- Write code to call the `Hello` function, then print the function's return value.
  logseq.order-list-type:: number
  ```go
  package main
  
  import (
      "fmt"
      "example.com/greetings"
  )
  
  func main() {
      // Get a greeting message and print it.
      message := greetings.Hello("Gladys")
      fmt.Println(message)
  }
  ```
	- In this code, you:
	  logseq.order-list-type:: number
		- Declare a main package. In Go, code executed as an application must be in a *main* package.
		  logseq.order-list-type:: number
		- Import two packages: `example.com/greetings` and the `fmt` package. This gives your code access to functions in those packages. Import `example.com/greetings` gives you access to the `Hello` function.
		  logseq.order-list-type:: number
		- Get a greeting by calling the `greetings` package's `Hello` function.
		  logseq.order-list-type:: number
- Edit the `example.com/hello` module to use your local `example.com/greetings` module.
  logseq.order-list-type:: number
	- logseq.order-list-type:: number
	  > Note: For production use, you'd publish the `example.com/greetings` module from its repository, where Go tools could find it to download it.
	- To do that, use the `go mod edit command` to edit the `example.com/hello` module to redirect Go tools from its module path top the local directory.
	  logseq.order-list-type:: number
	  ```
	  $ go mod edit -replace example.com/greetings=../greetings
	  ```
	- From the command prompt in the hello directory, run the `go mod tidy command` to synchronize the example.com/hello module's dependencies, adding those required by the code, but not yet tracked in the module.
	  logseq.order-list-type:: number
	  ```
	  $ go mod tidy
	  ```
- At the command prompt in the *hello* directory, run your code to confirm that it works.
  logseq.order-list-type:: number
  ```
  $ go run .
  Hi, Gladys. Welcome!
  ```
- ---
- ## Return and handle an error
  Handling errors is an essential feature of solid code.
-
- In `greetings/greetings.go`, add the next code.
  logseq.order-list-type:: number
  
  There's no sense sending a greeting back if you don't know who to greet. Return an error to the caller if the name is empty. Copy the following code into `greetings.go` and save the file.
  
  ```go
  package greetings
  
  import (
      "fmt"
      "errors"
  )
  
  func Hello(name string) (string, error) {
  
      if name == "" {
          return "", errors.New("empty name")
      }
  
      message := fmt.Sprintf("Hi, %v. Welcome!", name)
      return message, nil
  }
  ```
	- In this code, you:
	  logseq.order-list-type:: number
		- Change the function so that it returns two values: a `string` and an `error`. Your caller will check the second value to see if an error occurred. (Any Go function can return multiple values.)
		  logseq.order-list-type:: number
		- Import the Go standard library `errors` package so you can use its `errors.New` function.
		  logseq.order-list-type:: number
		- Add an if statement to check for a invalid request (an empty string where the name should be) and return an error if the request is invalid. The `errors.New` function returns `error` with your message inside.
		  logseq.order-list-type:: number
		- Add `nil` (meaning no error) as a second value in the successful return. That way, the caller can see that the function succeeded.
		  logseq.order-list-type:: number
- In your `hello/hello.go` file, handle the error now returned by the `Hello` function, along with the non-error value.
  logseq.order-list-type:: number
  
  Write the following code into `hello.go`.
  
  ```go
  package main
  
  import (
      "fmt"
      "log"
      "example.com/greetings"
  )
  
  func main() {
  
      log.SetPrefix("greetings: ")
      log.SetFlags(0)
  
      message, err := greetings.Hello("")
  
      if err != nil {
          log.Fatal(err)
      }
  
      fmt.Println(message)
  }
  ```
	- In this code, you:
	  logseq.order-list-type:: number
		- Configure the `log` package to print the command name ("greetings: ") at the start of its log messages, without a time stamp or source file information.
		  logseq.order-list-type:: number
		- Assign both of the `Hello` return values, including the `error`, to variables.
		  logseq.order-list-type:: number
		- Change the `Hello` argument from Gladys's name to an empty string, so you can try out your error-handling code.
		  logseq.order-list-type:: number
		- Look for a non-nil `error` value. There's no sense continuing in this case.
		  logseq.order-list-type:: number
		- Use the functions in the standard library's `log` package to output error information. If you get an error, you use the `log` package's `Fatal` function to print the error and stop the program.
		  logseq.order-list-type:: number
- At the command line in the `hello` directory, run `hello.go` to confirm that code works.
  logseq.order-list-type:: number
  
  ```
  $ go run .
  greetings: empty name
  exit status 1
  ```
- That's the common error handling in Go: Return an error as a value so the caller can check for it.
- ---
- ## Return a random greeting
  
  In this section, you'll change your code so that instead of returning a single greeting every time, it returns one of several predefined greeting messages.
-
- To do this, you'll use a Go slice. A slice is like an array, except that its size changes dynamically as you add and remove items. The slice is one of the Go's most useful types.
  
  You'll add a small slice to contain three greeting messages, then have your code return one of the messages randomly.
-
- In `greetings/greetings.go` change your code so it looks like the following.
  logseq.order-list-type:: number
  
  ```go
  package greetings
  
  import (
      "fmt"
      "errors"
      "math/rand"
  )
  
  func Hello(name string) (string, error) {
  
      if name == "" {
          return "", errors.New("empty name")
      }
  
      message := fmt.Sprintf(randomFormat(), name)
      return message, nil
  }
  
  func randomFormat() string {
  
      formats := []string {
          "Hi, %v. Welcome!",
          "Greet to see you, %v!",
          "Hail, %v! Well met!",
      }
  
      return formats[rand.Intn(len(formats))]
  }
  ```
	- In this code, you:
	  logseq.order-list-type:: number
		- Add a `randomFormat` function that returns a randomly selected format for a greeting message. Note that `randomFormat` starts with a lowercase letter, making it accessible only to code in its own package (in other words, it's not exported).
		  logseq.order-list-type:: number
		- In `randomFormat`, declare a `formats` slice with three message formats. When declaring a slice, you omit its size in the brackets, like this: `[]string`. This tells Go that the size of the array underlying the slice can be dynamically changed.
		  logseq.order-list-type:: number
		- Use the `math/rand` package to generate a random number for selecting an item from the slice.
		  logseq.order-list-type:: number
		- In `Hello`, call the `randomFormat` function to get a format for the message you'll return, then use the format and name value together to create the message.
		  logseq.order-list-type:: number
		- Return the message (or an error) as you did before.
		  logseq.order-list-type:: number
- In `hello/hello.go`, change your code so it looks like the following.
  logseq.order-list-type:: number
  
  ```go
  package main
  
  import (
      "fmt"
      "log"
      "example.com/greetings"
  )
  
  func main() {
  
      log.SetPrefix("greetings: ")
      log.SetFlags(0)
  
      message, err := greetings.Hello("Emilio")
  
      if err != nil {
          log.Fatal(err)
      }
  
      fmt.Println(message)
  }
  ```
- At the command line, in the `hello` directory, run `hello.go` to confirm that the code works.
  logseq.order-list-type:: number
  Run multiple times, noticing that the greetings changes.
  
  ```
  $ go run .
  Great to see you, Emilio!
  
  $ go run .
  Hi, Emilio. Welcome!
  
  $ go run .
  Hail, Emilio! Well met!
  ```
- ---
- ## Return greetings for multiple people
  In the last changes you'll make to your module's code, you'll add support for getting greetings for multiple people in one request. In other words, you'll handle a multiple-value input, then pair values in that input with a multiple-value output. To do this, you'll need to pass a set of names to a function that can return a greeting for each of them.
-
- But there's a hitch. Changing the `Hello` functions's parameter from a single name to a set of names would change the function's signature. If you had already published the `example.com/greetings` module users had already written code calling `Hello`, that change would break their programs.
  
  In this situation, a better choice is to write a new function with a different name. The new function will take multiple parameters. That preserves the old function for backward compatibility.
-
- In `greetings/greetings.go`, change your code so it looks like the following. (Add this function)
  logseq.order-list-type:: number
  
  ```go
  func Hellos(names []string) (map[string]string, error) {
      // A map to associate names with messages.
      messages := make(map[string]string)
      // Loop through the received slice of names, calling
      // the Hello function to get a message for each name.
      for _, name := range names {
          message, err := Hello(name)
          if err != nil {
              return nil, err
          }
          // In the map, associate the retrieved message with
          // the name.
          messages[name] = message
      }
      return messages, nil
  }
  ```
	- In this code, you:
	  logseq.order-list-type:: number
		- Add the `Hellos` function whose parameter is a slice of names rather than a single name. Also, change one of its return types from a `string` to a `map` so you can return names mapped to greeting messages.
		  logseq.order-list-type:: number
		- Have the new `Hellos` function call the existing `Hello` function. This helps reduce duplication while also leaving both functions in place.
		  logseq.order-list-type:: number
		- Create a `messages` map to associate each of the received names (as a key) with generated message (as a value). In Go, you initialize a map with the following syntax: `make(map[key-type]value-type)`. You have the `Hellos` function return this map to the caller.
		  logseq.order-list-type:: number
		- Loop through the names your function received, checking that each has a non-empty value, then associate a message with each. In this `for` loop, `range` returns two values: the index of the current item in the loop and the copy of the item's value. You don't need the index, so you use the Go blank identifier (an underscore) to ignore it.
		  logseq.order-list-type:: number
- In your `hello/hello.go` calling code. pass a slice of names, then print the contents of the `names/messages` map you get back.
  logseq.order-list-type:: number
  
  In `hello.go`, change your code so it looks like the following.
  
  ```go
  package main
  
  import (
      "fmt"
      "log"
      "example.com/greetings"
  )
  
  func main() {
  
      log.SetPrefix("greetings: ")
      log.SetFlags(0)
  
      names := []string{"Gladys", "Samantha", "Emilio"}
  
      messages, err := greetings.Hellos(names)
      if err != nil {
          log.Fatal(err)
      }
  
      fmt.Println(messages)
  }
  ```
	- With this changes, you:
	  logseq.order-list-type:: number
		- Create a `names` variable as a slice type holding three names.
		  logseq.order-list-type:: number
		- Pass the `names` variable as the argument to the `Hellos` function.
		  logseq.order-list-type:: number
- At the command line, change to the directory that contains `hello/hello.go`, then use go run to confirm that the code works.
  logseq.order-list-type:: number
  
  The output should be a string representation of the map associating names with messages, something like the following:
  
  ```
  $ go run .
  map[Emilio:Hail, Emilio! Well met! Gladys:Greet to see you, Gladys! Samantha:Hi, Samantha. Welcome!]
  ```
- ---
- ## Add a test
  Testing your code during development can expose bugs that find their way in as you make changes. In this topic, you add a test for the `Hello` function.
-
- Go's built-in support for unit testing makes it easier to test as you go. Specifically, using naming conventions, Go's `testing` package, and the *go test command*, you can quickly write and execute tests.
	-
	- In the *greetings* directory, create a file called *greetings_test.go*.
	  logseq.order-list-type:: number
	  
	  Ending a file's name with *_test.go* tells the *go test command* that this file contains test functions.
	- In *greetings_test.go*, write the following code and save the file.
	  logseq.order-list-type:: number
	  
	  ```go
	  package greetings
	  
	  import (
	      "testing"
	      "regexp"
	  )
	  
	  func TestHelloName(t *testing.T) {
	      name := "Gladys"
	      want :=regexp.MustCompile(`\b`+name+`\b`)
	      msg, err := Hello("Gladys")
	      if !want.MatchString(msg) || err != nil {
	          t.Fatalf(`Hello("Gladys") = %q, %v, want match for %#q, nil`, ms>
	      }
	  }
	  
	  func TestHelloEmpty(t *testing.T) {
	      msg, err := Hello("")
	      if msg != "" || err == nil {
	          t.Fatalf(`Hello("") = %q, %v, want "", error`, msg, err)
	      }
	  }
	  ```
		- In this code, you:
		  logseq.order-list-type:: number
			- Implement test functions in the same package as the code you're testing.
			  logseq.order-list-type:: number
			- Create two test functions to test the `greetings.Hello` function. Test function names have the form *TestName*, where *Name* says something about the specific test. Also, test functions take a pointer to the `testing` package's `testing.T` type as a parameter. You use this parameter's methods for reporting and logging from your test.
			  logseq.order-list-type:: number
			- Implement two test:
			  logseq.order-list-type:: number
				- `TestHelloName` calls the `Hello` function, passing a name value with which the function should be able to return a valid response message. If the call returns an error or an unexpected response message (one that doesn't include the name you passed in), you use the `t` parameter's `Fatalf` method to print a message to the console and end execution.
				  logseq.order-list-type:: number
				- `TestHelloEmpty` calls the `Hello` function with an empty string. This test is designed to confirm that your error handling works. If the call returns a non-empty string or no error, you use the `t` parameter's `Fatalf` method to print a message to the console and execution.
				  logseq.order-list-type:: number
	- At the command line in the greetings directory, run the *go test command* to execute the test.
	  logseq.order-list-type:: number
	  
	  The *go test command* executes test functions (whose names begin with `Test`) in test files (whose names end with *_test.go*). You can add the -v flag to get verbose output that list all of the test and their results.
	  
	  The tests should pass.
	  
	  ```
	  $ go test
	  PASS
	  ok      example.com/greetings   0.364s
	  
	  $ go test -v
	  === RUN   TestHelloName
	  --- PASS: TestHelloName (0.00s)
	  === RUN   TestHelloEmpty
	  --- PASS: TestHelloEmpty (0.00s)
	  PASS
	  ok      example.com/greetings   0.372s
	  ```
	- Break the `greetings.Hello` function to view a failing test.
	  logseq.order-list-type:: number
	  
	  The `TestHelloName` test function checks the return value for the name you specified as a `Hello`function parameter. To view a failing test result, change the `greetings.Hello` function so that no longer includes the name.
	  
	  In *greetings/greetings.go*, paste the following code in place of the `Hello` function.
	  Note that the highlighted lines change the value that the function returns, as if the *name* argument had been accidentally removed.
	  
	  ```go
	  // Hello returns a greeting for the named person.
	  func Hello(name string) (string, error) {
	      // If no name was given, return an error with a message.
	      if name == "" {
	          return name, errors.New("empty name")
	      }
	      // Create a message using a random format.
	      // message := fmt.Sprintf(randomFormat(), name)
	      message := fmt.Sprint(randomFormat())
	      return message, nil
	  }
	  ```
	- At the command line in the greetings directory, run *go test* to execute the test.
	  logseq.order-list-type:: number
	  
	  This time, run *go test* without the *-v* flag. the output will include results for only the test that failed, which can be useful when you have a lot of test. The `TestHelloName` test should fail -- `TestHelloEmpty` still passes.
	  
	  ```
	  $ go test
	  --- FAIL: TestHelloName (0.00s)
	      greetings_test.go:15: Hello("Gladys") = "Hail, %v! Well met!", <nil>, want match for `\bGladys\b`, nil
	  FAIL
	  exit status 1
	  FAIL    example.com/greetings   0.182s
	  ```
- ---
- ## Compile and install the application
  While the *go run* command is a useful shortcut for compiling and running a program when you're making frequent changes, it doesn't generate a binary executable.
-
- This topic introduces two additional commands for building code:
	- The *go build* command compiles the packages, along with their dependencies, but it doesn't install the results.
	- The *go install* command compiles and installs the packages.
-
- From the command line in the hello directory, run the *go build* command to compile the code into an executable.
  logseq.order-list-type:: number
  
  ```
  $ go build
  ```
- From the command line in the hello directory, run the new *hello* executable to confirm that the code works.
  logseq.order-list-type:: number
  
  Note that your result might differ depending on whether you changed your *greetings.go* code after testing it.
  
  ```
  $ ./hello
  map[Darrin:Great to see you, Darrin! Gladys:Hail, Gladys! Well met! Samantha:Hail, Samantha! Well met!]
  ```
	- You've complied the application into an executable so you can run it. But to run it currently, your prompt needs either to be in the executable's directory, or to specify the executable's path.
	  logseq.order-list-type:: number
	  
	  Next, you'll install the executable so you can run it without specifying its path.
- Discover the Go install path, where the go command will install the current package.
  logseq.order-list-type:: number
  
  You can discover the install path by running the *go list* command, as in the following example:
  
  ```
  $ go list -f '{{.Target}}'
  ```
	- For example, the command's output might say */home/gopher/bin/hello*, meaning that binaries are installed to */home/gopher/bin*. You'll need this install directory in the next step.
	  logseq.order-list-type:: number
- Add the Go install directory to your system's shell path.
  logseq.order-list-type:: number
  
  That way, you'll be able to run your program's executable without specifying where the executable is.
  
  ```
  $ export PATH=$PATH:/path/to/your/install/directory
  ```
- Once you've install updated the shell path, run the *go install* command to compile and install the package.
  logseq.order-list-type:: number
  
  ```
  $ go install
  ```
- Run your application by simply typing its name. To make this interesting, open a new command prompt and run the *hello* executable name in some other directory.
  logseq.order-list-type:: number
  
  ```
  $ hello
  map[Darrin:Hail, Darrin! Well met! Gladys:Great to see you, Gladys! Samantha:Hail, Samantha! Well met!]
  ```
 The goal was simple: run the binary, enter the correct password, and get
 the message "Correct!". But the password wasnt obviousit had to be
 uncovered by understanding the logic behind the compiled file.
 When I tried to execute the file (Compiled-1688545393558.Compiled), it
 just responded with "Try again!". So I opened it in Ghidra to inspect its
 internal logic. Inside the decompiled `main` function, I found an
 interesting line:
 __isoc99_scanf("DoYouEven%sCTF", local_28);
 This meant the program was expecting a string that starts with
 "DoYouEven", ends with "CTF", and extracts everything in between into the
 variable `local_28`. Then it compared `local_28` with two strings:
 `__dso_handle` and `_init`. If the input matched `_init`, the program
 would print "Correct!".
 To confirm my understanding, I recreated the logic in a simple C program:
 #include <stdio.h>
int main() {
 char local_28[32];
 printf("Password: ");
 scanf("DoYouEven%sCTF", local_28);
 printf("Input: %s\n", local_28);
 return 0;
 }
 After compiling and running it, inputting `DoYouEven_initCTF` printed
 `_init`, as expected. Then I added logic to check if the input matched
 "_init":
 #include <stdio.h>
 #include <string.h>
 int main() {
 char local_28[32];
 printf("Password: ");
 scanf("DoYouEven%sCTF", local_28);
 if (strcmp(local_28, "_init") == 0) {
 printf("Correct!\n");
 } else {
 printf("Try again!\n");
 }
 return 0;
}
 After compiling, the behavior exactly matched the original binary.
 Entering `DoYouEven_initCTF` resulted in "Correct!".
 This project gave me a strong hands-on start with reverse engineering. I
 learned how to work with Ghidra, analyze binary logic, and simulate the
 same functionality in my own code. It was especially fun to see how scanf
 with format strings can be used to conceal logic and trick the user. This
 may have been a small challenge, but it gave me the confidence and
 curiosity to explore more complex binaries

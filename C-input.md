# A comprehensive guide to C Input/Output
# 1. **`Scanf()`**  


 1. `scanf` is a function used to read input from the console or standard input. Declaratioin of `scanf()` is  
   ```C
   int scanf(const char *format, ...);
   ```  
  format is the `scanf` format string. It iterates each characters of user provided string and stops at "%". Now `scanf` reads a line from stdin. User's input comes as a string. It converts string to char, int, long, float, double and sets the value of the pointer located at the argument. In care of string it simply copies the string to the output.  
   
   2. `scanf` function returns the number of successful inputs scanned and assigned. It returns zero in case of matching failure occurs and EOF is returned if the input is reached the end.  
   ```C
   printf("%d",scanf("%d%d",a,b));
   ```
   > 2  
   
  Here `scanf` will return 2 if the two values **a** and **b** match the format specifier i.e, both are integers. If a is character then the ouput will be 0 as the `scanf` will stop reading there. If a is integer and b is character output will be 1.  

  3. If the format string is  
   ```C
   "%7d%s %c%lf"
   ```  
 The above format string scans the first seven characters as a decimal integer, then reads the remaining as a string until a space, newline, or tab is found, then consumes whitespace until the first non-whitespace character is found, then consumes that character, and finally scans the remaining characters as a double.  
  * Note: `%5s` would take a string of at most 5 non white-space characters as input.

 4. Caution when reading char with `scanf`  
   ```C
   char a,b,c;
   scanf("%c",&a);
   scanf("%c",&b);
   printf("2nd scan=%c\n",b);
   scanf("%c",&c);
   printf("3rd scan=%c",c);
   ```  
 If I try to input a,b,c by pressing *enter* after each character I get the following result
> Ouput:  
> a  
2nd scan=  
>  
> b  
3rd scan=b

 As soon as I put `a` and press *enter*, I am directed to the third `scanf` prompt. First `scanf` will read 'a' , the second `scanf` will read '\n', the newline character, which came from the *enter* pressed by me.  
 This doesn't happen with `%d` format specifier because `%d` automatically eats whitespaces and special characters but for `%c` whitespaces, newline and special characters are characters and it have to interpret them as characters.  
 To fix this `scanf` issue we have to leave a space before `%c`, that way `scanf` automatically eat whitespaces and special characters. Or we can put `getchar()` after every scanf to eat trailing newline.  

 5. **`'*'` in `scanf()`**: If you need to exclude some characters or number from input use `*` before after `%` and before format specifier. Suppose, the input is date 25/01/2020 and you want to get day, month, year separately as integers. You can do this by  
   ```C
   scanf("%d%*c%d%*c%d,&day,&month,&year);
   ```
* Note: `%*s` will exclude one word  
 6. **Scanset:** `scanf` will process only those characters which are part of scanset. We can define scanset by putting characters inside squre brackets.  
    * `%[0-9]s` would take just number characters as input. If you write `"1234abc567"` in the console, then only `"1234"` would be taken as input.  
    * `%[a-z]s` would take just small letter alphabets as input.  
    * `%[123]s` would take input until a character other than 1,2, or 3 is encountered.  
    * If first character of scanset is `'^'` then the specifier will stop reading after first occurence of that character.  
    * `%[^\n]s` would read all characters but stops after first ocurrence of `'\n'` i.e, the newline character. Hence, this scanset can be used to read lines even if they have white-space in them.  
  
 7. Consider the following code  
  ```C
  int n;
  scanf("%d\n",&n);
  printf("you typed %d\n",n);
  ```  
Here is a seemingly innocent code to read an integer with `scanf`, but the `printf` statement will not be displayed until we press a non white-space character. This is due to the `'\n'` in `scanf`. `'\n'` or `' '` in format string means to read characters until it finds a non-whitespace character, and it may need to read another line before it can find that non-whitespace character.  
You can rectify this by using `scanf("%d%*c",&n)`, the `c` will tell the compiler to ignore the newline character.  

 8. **Reading a line from console using `scanf`**  
      * To read a line from console we can use `scanf("%[^\n]",str)` but it's dangerous as it doesn't have buffer overflow protection.  
      * To fix the buffer overflow problem in above code we can use this `scanf("%265[^\n]%*c",str)`. 265 is the size of the buffer-1. `%*c` is used to discard any newline character.  
      * A better way to read lines in C is to use `fgets()`, using `gets()` and `scanf()` is highly disliked due to a chance of buffer overflow. `fgets()` is usually used to read the line and parse it using `sscanf()`.  
  
  9. The correct way to input line from stdin is to use `fgets()`. The syntax of `fgets()` is   
   `char *fgets(char *str, int n, FILE *stream)`  
   Here, `str` is is the pointer to array of chars where the string read is stored. `n` is the maximum number of characters to be read, usually, the length of array is used. `stream` is pointer to file object that identifies the stream where characters are read from, for console input `stdin` is used. The `fgets` function reads at most one less than the number of characters speciﬁed by `n` from the stream.  No additional characters are read after a new-line character (which is retained) or after end-of-ﬁle.  
   `fgets` returns `str`, if end-of-file is encountered or no characters have been read it returns a null pointer.  
   ```C
   fgets(a,sizeof(a),stdin);
   size_t n = strlen(a);
   if(n>0 && a[n-1] == '\n')
   a[n-1] = '\0';
   ```  
   Here, extra lines below `fgets` are used to remove trailing newlines.  
   
  10. To read a variable length line we will need dynamic memory management to increase the buffer size each time it overflows. For more info [see this](https://stackoverflow.com/questions/314401/how-to-read-a-line-from-the-console-in-c).
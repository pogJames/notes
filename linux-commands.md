1. `ls` lists files in the current directory
```bash
james_try_ubuntu@JamesZenbook:~$ ls
coffee_mqtt  new  rusting  try-cpu

-l (long format)
-a (show hidden)
-h (human-readable sizes)
-t (sort by time)
```
2. `pwd` prints the current working directory
```bash
james_try_ubuntu@JamesZenbook:~$ pwd
/home/james_try_ubuntu
```
3. `mkdir` creates a new directory
```bash
james_try_ubuntu@JamesZenbook:~$ mkdir okay

-p (create parent dirs as needed, no error if exists)
-v (verbose)
```
4. `cd` changes the current directory *or* go to the specified directory
```bash
james_try_ubuntu@JamesZenbook:~$ cd okay
james_try_ubuntu@JamesZenbook:~okay$

- previous directory
.. parent directory
```
5. `git` version control
6. `sudo` run a command with a administrator authority
```bash
james_try_ubuntu@JamesZenbook:~/try-cpu/okay$ sudo apt update
```
7. `rm` remove files
```bash
james_try_ubuntu@JamesZenbook:~$ rm okay.py

-r recursive for directories
-f force, no prompt
-v verbose (visualization)
```
8. `cp` copy files
```bash
james_try_ubuntu@JamesZenbook:~$ cp exp.py new/exp.py

-r (recursive)
-v (verbose)
-p (preserve permissions)
-i (prompt before overwrite)
```
9. `mv` move or rename files
```bash
james_try_ubuntu@JamesZenbook:~$ mv exp.py expo.py
james_try_ubuntu@JamesZenbook:~$ sudo mv expo.py /new

-i (prompt on overwrite)
-v (verbose)
```
10. `cat` view file contents
```bash
james_try_ubuntu@JamesZenbook:~$ cat expo.py
print("hello world")
```
11. `grep` search for text/patterns inside files
```bash
james_try_ubuntu@JamesZenbook:~/new/MQTT$ grep "random" pub.py
import random
        base_temp += random.uniform(-0.5, 1.5)

-r (recursive)
-i (case-insensitive)
-n (line numbers)
-v (invert match)
```
12. `vim/nano/ve` edit files
```bash
james_try_ubuntu@JamesZenbook:~/new/MQTT$ vim pub.py
```
13. `find`

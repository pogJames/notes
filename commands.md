1. create + activate venv
```
python -m venv .venv
source .venv/bin/activate # macOS/Linux
venv\Scripts\activate # Windows
```
2. compress current files/folders
```
tar -czvf <project>.tar.gz *
```
3. transfer files/folders
```
scp -r project/path root@192.168.1.<address>:/root/
```
4. extract files/folders
```
tar xzvf <project>.tar.gz -C /path/to/destination
```
5. move/rename folder
```
mv /old_folder /new_folder
mv file.txt /newfolder
mv file.txt file2.txt
```

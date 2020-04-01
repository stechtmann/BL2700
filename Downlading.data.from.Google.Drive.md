This will help you download data from Google Drive

## Get the gdown.pl script

### Prepare a directory
```{BASH}
cd ~
mkdir software
cd software
```
### Download the gdown.pl script
```{BASH}
wget https://raw.githubusercontent.com/circulosmeos/gdown.pl/master/gdown.pl
```
## convert the gdown.pl script to an executable.
```{BASH}
chmod +x gdown.pl
```
## add the path to the gdown.pl script to your path
Make a copy of your current .bashrc
```{BASH}
cp ~/.bashrc ~/old.bashrc
```
Add a new line to your ~/.bashrc file
```{BASH}
echo 'export PATH="~/software/:$PATH"' >> ~/.bashrc 
```
Activate the changes

```{BASH}
source ~/.bashrc
```

## Download your data

to download data you need to know the google id for the file that you're interested in downloading.  You can find this by getting the sharable link for the file of interest.  

[https://drive.google.com/a/mtu.edu/file/d/1xGe2jy41T9KX8vx3CV-RFRG8n2f4qGVt/view?usp=sharing]()

Within this file there is the file id shown below in bold
[https://drive.google.com/a/mtu.edu/file/d/**1xGe2jy41T9KX8vx3CV-RFRG8n2f4qGVt**/view?usp=sharing]()

This link however, will not allow you to download, it will just allow you to view.  So you need to use that ID in the edit url

[https://drive.google.com/file/d/**GoogleFileIDGoesHere**/edit]() 

This can now be used in the gdown.pl command shown below for the ID shown above.

```{BASH}
gdown.pl https://drive.google.com/file/d/1xGe2jy41T9KX8vx3CV-RFRG8n2f4qGVt/edit NAME_OF_OUTPUT_FILE.fastq.gz
```

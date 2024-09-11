# Jenkins-Active-Reactive-Parameter

### In this task, I have created two parameters in Jenkins pipeline.
### One parameter, lists all the folder in the S3 bucket and other parameter takes the reference from the first parameter and lists all the files in that folder.

### First of all, create a pipeline > go to configuration > Select "This project is parameterised"

```
Name: FOLDER_LIST
```
### Select Groovy Script

```
def command = ['bash', '-c', '''
    aws s3api list-objects --bucket kauserbucket --region us-east-2 --query 'Contents[].{Key: Key}' --output text | grep '/' | awk -F'/' '{print $1}' | sort | uniq ''']
def list_command = command.execute()
def FOLDER_LIST = list_command.text.tokenize()
return FOLDER_LIST
```
```
Choice Type: Single Select
Filter starts at: 1
```

### Add Parameter: Active Choices Reactive Parameter
### Select Groovy Script

```
def greeting = "${FOLDER_LIST}"

def command2 = ['bash', '-c', """
       aws s3api list-objects --bucket kauserbucket --region us-east-2 --query "Contents[?starts_with(Key, '${greeting}')].Key" --output text | awk -v prefix='${greeting}' '{sub(prefix, "", \$0); print}' | tr '\t' '\n' | sed 's|.*/||' | sed 's/.sh\$//'
     """]

def process = command2.execute()
def OBJECT_LIST = process.text.tokenize('\n')

return OBJECT_LIST
```

```
Choice Type: Single Select

Reference parameters: FOLDER_LIST

Filter Starts At: 1
```
```
Apply
Save
```

## Thank you!
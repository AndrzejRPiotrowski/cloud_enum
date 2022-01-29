# osint_cloud
Multi-cloud OSINT tool (python). Find public resources in AWS, Azure, and Google Cloud.

Currently it works only with the following Cloud:

**Amazon Web Services**:
- Open / Protected S3 Buckets
- awsapps (WorkMail, WorkDocs, Connect, etc.)

**Microsoft Azure**:
- Storage Accounts
- Open Blob Storage Containers
- Hosted Databases
- Virtual Machines
- Web Apps

**Google Cloud Platform**
- Open / Protected GCP Buckets
- Open / Protected Firebase Realtime Databases
- Google App Engine sites
- Cloud Functions (enumerates project/regions with existing functions, then brute forces actual function names)

# Usage

## Setup
Several non-standard libaries are required to support threaded HTTP requests and dns lookups. 

```sh
pip3 install -r ./requirements.txt
```

## Running
The only required argument is at least one keyword. You can use the built-in fuzzing strings, but you will get better results if you supply your own with `-m` and/or `-b`.

You can provide multiple keywords by specifying the `-k` argument multiple times.

Keywords are mutated automatically using strings from `osint_cloud/fuzz.txt` or a file you provide with the `-m` flag. Services that require a second-level of brute forcing (Azure Containers and GCP Functions) 

## Example
Let's say you were researching "google" whose website is "google.io" that makes a product called "google". You could run the tool like this:

```sh
./cloud_enum.py -k google -k google.io -k google
```

HTTP scraping and DNS lookups use 5 threads each by default. You can try increasing this, but eventually the cloud providers will rate limit you. Here is an example to increase to 10.

```sh
./cloud_enum.py -k keyword -t 10
```


**Complete Usage Details**
```
usage: cloud_enum.py [-h] -k KEYWORD [-m MUTATIONS] [-b BRUTE]

Multi-cloud enumeration utility. All hail OSINT!

optional arguments:
  -h, --help            show this help message and exit
  -k KEYWORD, --keyword KEYWORD
                        Keyword. Can use argument multiple times.
  -kf KEYFILE, --keyfile KEYFILE
                        Input file with a single keyword per line.
  -m MUTATIONS, --mutations MUTATIONS
                        Mutations. Default: osint_cloud/fuzz.txt
  -b BRUTE, --brute BRUTE
                        List to brute-force Azure container names. Default:
                        osint_cloud/fuzz.txt
  -t THREADS, --threads THREADS
                        Threads for HTTP brute-force. Default = 5
  -ns NAMESERVER, --nameserver NAMESERVER
                        DNS server to use in brute-force.
  -l LOGFILE, --logfile LOGFILE
                        Will APPEND found items to specified file.
  --disable-aws         Disable Amazon checks.
  --disable-azure       Disable Azure checks.
  --disable-gcp         Disable Google checks.
  -qs, --quickscan      Disable all mutations and second-level scans

```
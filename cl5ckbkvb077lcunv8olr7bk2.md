---
title: "A Fast, Secure Cloud Storage Sync For Linux"
seoTitle: "sync files to cloud in linux"
seoDescription: "Stroj allow users to upload files and folders to cloud for free up to 150 GB. This also provides cli utility which make easier to sync file in linux."
datePublished: Fri Jul 08 2022 14:38:04 GMT+0000 (Coordinated Universal Time)
cuid: cl5ckbkvb077lcunv8olr7bk2
slug: a-fast-secure-cloud-storage-sync-for-linux
canonical: https://flarexes.com/a-fast-secure-cloud-storage-sync-for-linux
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1657289407261/5P4yJXYLl.png
tags: cloud, linux, sync, cloud-storage, storj

---

If you're a regular user of Linux or just switched from Windows recently, then you may have encountered the cloud storage problem. In Windows, there are plenty of cloud storage programs like OneDrive which help you to sync your data to the cloud. But, unfortunately in Linux, we don't have such an option for now and some of them are paid like NextCloud.

So, recently I found a service [Storj](https://www.storj.io/). Storj is a Decentralized storage provider with a Zero-Trust security model. Storj gives you the vibe of AWS S3 because it is not like a typical cloud service like Google Drive or DropBox. It's a bit different, let's discuss. In cloud services like Google Drive, OneDrive or Dropbox you have to pay a monthly subscription fee and its file structure is relatively the same as your typical system. There would be some directories containing files or sub-directories.

But, In cloud storage providers like AWS, GCS, Azure And Storj you'll only pay for what you use. For example, If you are using 56 Gigs then you would only pay for that rather than any storage you aren't using. And the concept of the file structure is a little bit changed here. You'll need to create a bucket similar to a folder which contain all the folders and files. And, Stored files and directories in a bucket are called Objects. Every single bucket has its own key to unlock it.

There are some other differences which we won't gonna look at here. That was just some kind of technical explanation that you don't need to worry about.

## Why Storj?

Yes, Storj does not provide an auto-sync facility like OneDrive. But It provides a faster way to backup data from CLI utility. We don't need to use the bloated web page to upload files anymore. I want to backup everything as simply as I can and that's what Storj does.

Then, why don't we use AWS S3? That also provides AWS CLI utility because It's not free and cheap. It is expensive... But you know what, Storj provides 150 GB for free and If you wanna expand then it's cheaper than AWS.

One more reason is **Decentralization**.

## Storj Privacy And Security

As I said earlier Storj follows the Zero-Trust security model which means do not trust anybody and verify everything every time. Storj is designed to be highly secure and that's what I love about them. They are very open to sharing how things work behind the scenes on their website for that check out they're [How It Works](https://www.storj.io/how-it-works) section.

**Here are some brief** Storj uses Industry-standard encryption **AES-256-GCM**. Which never have been broken till now. This is the same encryption used by the military and government to secure their rest or stored data. Your data is split into the decentralized network all over the world. So, If a few nodes get compromised or down then, It wouldn't affect your files. Storj has more than 14k nodes around the world to split your data. And, That makes it impossible even for Stroj to see your data.

So, If any hacker wants to access your data then he has to locate each node where your file pieces are present and put them in one place. Then, identifying your pieces from millions of object pieces will gonna be a pain for him. After that, he needs to break AES 256-bit encryption with GCM mode. That seems not easy in any way.

# Fastest Way To Use Storj

Step 1: Create A Storj Account And Sign In To That

Step 2: Create A Bucket To Store File And Folders And Store The Credentials to Unlock later

Step 3: Now Create S3 Access Grants To Access All Files From AWS CLI And Store The Credentials

![7.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1657289774899/k24PNhCGI.png align="left")

Step 4: Install AWS CLI From [AWS Official Site](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html). It's Easy to Install

Step 5: Now Configure AWS CLI. Type `aws configure` And Then Enter The S3 Credentials

```txt
┌─[radowoo@SecOS]─[~]
└──╼ $ aws configure 

AWS Access Key ID [None]: <<yourAccessKey>>
AWS Secret Access Key [bbxq]: <<yourSecretKey>>
Default region name [None]: 
Default output format [None]:
```

Now all set, it was just a one-time process. Now, you can upload, delete, and view from a bucket. So let's see some basic tasks.

**List files in a bucket**

```txt
aws s3 --endpoint-url=https://gateway.storjshare.io ls s3://<<Your Granted Bucket Name>>
```

`aws s3 --endpoint-url=https://gateway.storjshare.io` this syntax always be the same, so let's just alias that by putting the below line in `~/.zshrc` or `~/.bashrc`

```bash
alias storj='aws s3 --endpoint-url=https://gateway.storjshare.io'
```

Now, Restart your terminal.

**Uploading a file to bucket**

```txt
aws s3 --endpoint-url=https://gateway.storjshare.io cp <<Your File Location>> s3://<<Bucket Name>>
```

**Sync folder with bucket**

```txt
aws s3 --endpoint-url=https://gateway.storjshare.io cp <<Local Directory>> s3://<<Your Bucket>> --recursive
```

or

```txt
aws s3 --endpoint-url=https://gateway.storjshare.io sync <<Local Directory>> s3://<<Your Bucket>>
```

or we can use our alias

```plaintext
stroj sync <<Local Directory>> s3://<<Your Bucket>>
```

Much Better... Check Out More Command [Here](https://docs.storj.io/dcs/getting-started/gateway-mt/).

## More Way To Upload Files To Storj

* Uplink CLI
    
* Web Browser
    
* RClone
    
* Client Libraries (For Developers)
    

But, Why did we use AWS CLI? Because It's better and more flexible, Simple. And only **Uplink** uses end-to-end encryption for your data else sticks with server-side encryption. Now you can easily upload your documents and data to cloud storage.

# One Problem With Stroj

Stroj provides superb functionality for free (Till 150 GB at least). But Storj is a relevantly new player in the market with a different approach to storing data securely. There is always a risk of trust. Which may sometime knock you. In my case, I don't trust any cloud provider to store my confidential information. I usually use cloud storage to sync my projects and some non-confidential data.

So, I believe in future Stroj will gonna think about that. For now they do what they say

> A Fast, secure cloud storage at a fraction of the cost.

# Conclusion

Today, We have decided a better way to use cloud storage in Linux. We also have seen how Stroj securely stores our data. And, In future we may see a more sophisticated way to secure our data from hackers and big tech giant companies like Google. Now Privacy is becoming a concern to everyone. Other players like ProtonDrive, Which follow a similar fashion to secure data. They're also are coming into the league now. Which will give us 👇

<center><strong>A Fast, secure and suckless cloud storage for Linux Users</strong></center>
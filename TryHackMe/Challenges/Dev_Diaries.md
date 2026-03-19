# Dev Diaries | TryHackMe Write-up

## Introduction

Hi everyone, welcome to this OSINT-oriented room on [TryHackMe](https://tryhackme.com/room/devdiaries).

In this challenge, we are given very limited information: only a domain name — **marvenly.com**.
The goal is to investigate the developer’s activity and recover information about the development process, including a hidden flag.



## Scenario

> We have just launched a website developed by a freelance developer. The source code was not shared with us, and the developer has since disappeared without handing it over.
> Despite this, traces of the development process and earlier versions of the website may still exist online.



## Question 1

**What is the subdomain where the development version of the website is hosted?**

First, I tried accessing the main domain:

```
marvenly.com
```

But I got a connection error (site unreachable).

Since the main site was down, I moved to an OSINT approach and searched for subdomains using **certificate transparency logs** via crt.sh.

By searching for:

```
marvenly
```

I found several entries, including a very interesting subdomain:

**Answer:**

```
uat-testing.marvenly.com
```



## Question 2

**What is the GitHub username of the developer?**

Next step: identify the developer.

I searched for **"marvenly"** on GitHub. Among many results, one repository stood out:

* Repository name related to *marvenly*
* Mostly HTML content
* Created by a user named:

**Answer:**

```
notvibecoder23
```



## Question 3

**What is the developer's email address?**

To find the developer’s email, I checked the repository’s commit history.

I opened one of the latest commits:

> "Parking the domain until the issue is solved"

Then, I used a classic trick: adding `.patch` to the commit URL to reveal metadata.

Example:

```
https://github.com/notvibecoder23/marvenly_site/commit/<commit_id>.patch
```

This exposes additional details, including the author’s email.

**Answer:**

```
freelancedevbycoder23@gmail.com
```



## Question 4

**What reason did the developer mention in the commit history for removing the source code?**

By reviewing the commit messages, I found the explanation.

The developer mentioned that the project was abandoned due to an issue with payment.

**Answer:**

```
The project was marked as abandoned due to a payment dispute
```



## Question 5

**What is the value of the hidden flag?**

Interestingly, the flag can be found by exploring the commit history.

I checked an earlier commit titled:

> "removed my signature, ready for deployment"

Inside the code changes, I found a hidden HTML comment:

```html
<!-- removed the signature, but I'm leaving something as my hidden signature THM{g1t_h1st0ry_n3v3r_f0rg3ts} -->
```

**Answer:**

```
THM{g1t_h1st0ry_n3v3r_f0rg3ts}
```



## Conclusion 

This room is a great introduction to **OSINT + GitHub investigation techniques**:

* Using certificate transparency logs (crt.sh) to discover subdomains
* Identifying developers through GitHub reconnaissance
* Extracting hidden information from commit metadata
* Leveraging `.patch` files to reveal sensitive data
* Exploring commit history to uncover hidden secrets





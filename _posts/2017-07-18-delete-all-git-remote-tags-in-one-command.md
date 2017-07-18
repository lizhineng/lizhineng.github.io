---
layout: post
title: Delete All Git Remote Tags in One Command
categories: git
---

Today, I wanna refactor my project from the beginning of the git repository, because of the messy tags and branches, I thought I need to remove all the remote tags first.

## tl;dr

```bash
git ls-remote -q -t | awk '/^(.*)(\s+)(.*[^\^\{\}])$/ {print ":" $2}' | xargs git push origin
```

There is a pipe command, let's analyse it one by one.

## Get the remote tags list

The first we need to fetch the tag list by `git ls-remote`, and it shows all tags and head by default, add a flag `-t` that stands for tags to show the tags only.

Also, you will notice there is a remote URL printed at the first line, let's remove it also by the flag `-q`.

Combine together, we'll got `git ls-remote -q -t`, let's paste in terminal and give it a try.

```bash
e1fe6faadf6fe5b7af290367adc0cb1f56e6c5d4	refs/tags/v0.3.10
1e206054ce3d54059fadb35f1da06a8a81cc58b5	refs/tags/v0.3.10^{}
0b8762fa9a2160b0de1e85abe01648f16a013571	refs/tags/v3.0.0
d0f25936b7d99f40edcdbdf83c505c33861b8e96	refs/tags/v3.0.0^{}
8c74e7c2d2adc061f862a4332ff2993eebd66200	refs/tags/v3.0.1
a2b34f0534599b95b31abba518b357db99cd1258	refs/tags/v3.0.1^{}
d723baf4020fbde4e0092a6d498e5613ee07a5a4	refs/tags/v3.0.2
c878e73463e1cdd1502f6234c8badc09a98659b8	refs/tags/v3.0.2^{}
ca5c2bc9ebf1b10e67358ac94c614449d0f85a8b	refs/tags/v3.0.3
cbaf39df726414677c514e631b7ef8cfce42f23b	refs/tags/v3.0.3^{}
41f7aa1028740adfeceae43767b763e44f4cc54c	refs/tags/v3.0.4
32fa2d3e8810ebf5436b68b0ce4a3d770375b18d	refs/tags/v3.0.4^{}
46ad9d9ab51cb9ec606941d1ebd1f9b044171c5b	refs/tags/v3.0.5
4c5cef15fc4dab82a864a9a20bec62421bc07887	refs/tags/v3.0.5^{}
```

Seems great, right? Let's keep moving.

## Delete a single remote tag
That's pretty easy and have two types of way to delete a remote tag, such as:
```bash
git push origin :tagname
git push --delete origin tagname
```

Choose one you like, and let's pick up the first one in this article ;)

## Extract the tag name we need
We got the tags information from the step 1, that's a problem here, because we need to bulk delete those tags, we just need tag name but not the commit hash, so we need to use awk to extract the tag name we need.

### The regular expression meaning

Regular Expression | Explain
------------------ | -------
(.*) | Match the commit hash, any characters.
(\s+) | Match the white spaces between commit hash and tag name.
(.*[^\^\{\}]) | Match the tag name we need, and some tags will end with ^{}, we need to avoid it matching.

Combine the regular expression together, we got `/^(.*)(\s+)(.*[^\^\{\}])$/`. Then tell awk to print the format we need _:refs/tags/tagname_, therefore we print the $2 means that matching the tag name(including /refs/tags/) from the regular expression and add a colon ":" before it, so we could get the correct command syntax `:refs/tags/tagname`.

## Fire up
Now, we have the tag list we wanna delete, at the end, all we need to do is using **xargs** to bulk delete each tag in the list. Using pipeline, the tag name will put to the last automatically to execute the complete command: `git push origin :refs/tags/tagname`

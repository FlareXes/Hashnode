---
title: "Build Your Own Private Search Engine With SearXNG"
seoDescription: "SearXNG respects user's privacy and also provide high quality results like Google and Bing. We can easily run SearXNG instance using Docker in one command."
datePublished: Sat Apr 22 2023 03:02:52 GMT+0000 (Coordinated Universal Time)
cuid: clgreaw3c00010al4gy2c7uj0
slug: build-your-own-private-search-engine-with-searxng
canonical: https://flarexes.com/build-your-own-private-search-engine-with-searxng
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1682133028522/1e3c1886-1420-49da-9604-b9acc771610b.png
tags: docker, programming, security, devops, searxng

---

Browsers are usually expected to be the most used applications on PCs. However, people often forget that if browsers are the most used applications, then search engines should be second. Search engines like Google or Bing provide high-quality search results but not very good privacy policies. They track you, create a personalized profile about you, demote controversial information, and only show biased search results, which puts the jobs of journalists, activists, and others in a tough position regarding what is right or wrong.

In my search for a solution, I discovered [SearXNG](https://docs.searxng.org/). This provides all functionalities and better search results just like Google or Bing. SearXNG respects user privacy and delivers an extreme level of customization features.

# What is SearXNG & What Makes It So Different?

[**SearXNG**](https://docs.searxng.org/) is a **Open-Source** *metasearch* engine. SearXNG by default aggregates results from over 70 search services providers like Google, DuckDuckGO, Brave Search etc. Originally SearXNG is a fork of another Open Source project [SearX](https://searx.github.io/searx/) but it has a few key differences that you can check out in the "[Differences to searx](https://searx.github.io/searx/)" section on GitHub. One of the main reasons I use SearXNG is its **User Interface**, which is both sleek and functional. Additionally, SearXNG offers Docker support, making it easy to set up. But perhaps the best thing about SearXNG is its commitment to user privacy. Unlike some search engines, SearXNG doesn't track users or generate personalized profiles, and it never shares any information with third parties.

## Let's talk about what makes it different

To use SearXNG, you first need to host it. While there are [public instances](https://searx.space/) of SearXNG available, I recommend hosting your own instance on localhost to take full advantage of its features. By using public instances, you may be placing your trust in someone else with less control over it. However, setting up your own instance of SearXNG is a simple process, and it ensures that you aren't sharing any information with anyone, not even SearXNG itself.

Another advantage of SearXNG is that it is a metasearch engine, which means you can choose which search engines to use and which ones to avoid. In my case, I use DuckDuckGo, Brave Search, StartPage and Wikipedia. Because to get results like Google, I use StartPage and Wikipedia. To get Images results like Bing, I use DuckDuckGo. And, for unbiased purposes, I recommend and use Brave Search (A Independent Search Engine). You have over 130 options to choose from. So select one that suits your needs.

> **Metasearch Engine** : A metasearch engine is a search service that aggregates data from multiple search services. For example, DuckDuckGo gathers results from many sources, including Yahoo and Bing, in addition to its own search algorithms.

# SearXNG Setup With DOCKER

It's easy just follow the steps below

1. Create a directory where you want to store SearXNG configs
    
    ```bash
    mkdir SearXNG && cd SearXNG
    ```
    
2. Run the docker command
    
    ```bash
    docker run --read-only --security-opt=no-new-privileges --restart=unless-stopped --name="xng" \
    -d -p 8080:8080 -v "${PWD}/searxng:/etc/searxng" \
    -e "BASE_URL=http://localhost:8080/" \
    -e "INSTANCE_NAME=xng" searxng/searxng
    ```
    
    Now, Go to http://localhost:8080 in your browser. It should work properly. Now your directory structure should look like this:
    
    ```plaintext
     searxng
    └──  searxng
        ├──  settings.yml
        └──  uwsgi.ini
    ```
    
    ***Explanation of Docker command***
    
    * `read-only` flag restricts write access in the container.
        
    * `--security-opt=no-new-privileges` flag restricts gaining new privileges (Eg. can't use tools like sudo).
        
    
    > **Note :** The above flags are for security purposes and can be removed. Simply specifying they are optional.
    
    * `--restart=unless-stopped` flag restarts the container automatically on reboot.
        
    * `--name="xng"` flag specifies the name of the container so you don't have to worry about container ID.
        
    * `-d` flag is to run the container in the background.
        
    * `-p 8080:8080` flag maps the Host Port : Container Port.
        
    * `-v "${PWD}/searxng:/etc/searxng"` flag binds host volume (above directory structure) with container volume.
        
    * Else are environment variables.
        
3. Let's talk about `settings.yml`
    
    Changing the settings of SearXNG won't work as expected because SearXNG doesn't persist changes. Instead, you need to make changes in `settings.yml`. For the first time, you might face few problems reading through it. So, just do it once and save it somewhere so you don't have to do it again.
    
    Ex - SearXNG default response time is 3 seconds which many of you won't like so change it.
    
    from
    
    ```plaintext
    request_timeout: 3.0
    ```
    
    to
    
    ```plaintext
    request_timeout: 1.0
    ```
    
    ***Warning*** results might get effect because fetching data from different sites might take few seconds. For me, I can wait 3 seconds so I didn't change it.
    
    Or you can just use my [settings.yml](https://raw.githubusercontent.com/FlareXes/dotfiles/main/searxng/searxng/settings.yml) with caution.
    

Now you have your own private search instance. If you like to host a public search instance, Then you can check out this youtube video [build your own private search engine](https://www.youtube.com/watch?v=ifT6npY39Dw).

## SearXNG As Default Search Engine

* **Firefox**
    
    1. Go to `localhost:8080`
        
    2. Right-click on the address bar
        
    3. Select Add "SearXNG" from the context menu
        
    4. Now you can change your default search engine from browser settings
        
* **Chrome**
    
    1. Go to `localhost:8080`
        
    2. Right-click on the address bar
        
    3. Select "Manage search engines" from the context menu
        
    4. Select the Add button and fill out the fields for Search engine, Keyword, and URL with %s (Ex - http://localhost:8080/search?q=%s)
        
    5. Now you can change your default search engine from browser settings
        

# FAQ From Privacy And Security Perspective

**1\. How can SearXNG protect privacy if it exposes my IP ?**

Ans. Yes, If you're using SearXNG then it will expose your public IP address but with your IP they can not access any personal info except in which city you live. There are much better ways to track using cookies, URL referer etc. That SearXNG deducts automatically. Or If your threat model requires hiding your IP then you can host a public instance on the cloud. This way it won't reveal your IP. SearXNG can also be configured with proxy or TOR.

**2\. How is it different when it is just fetching data from the same search engines ?**

Ans. SearXNG doesn't store or send cookies to any search engines. It also doesn't store history and directly redirects to websites by deducting URL referer (can be used to track users). And of course, No Ads. SearXNG does not serve ads.

**3\. How SearXNG is unbiased? The results are still same.**

Ans. Yes, SearXNG aggregates data from different search engines that doesn't mean this could be biased. There is no way for Google to create a profile about you if you're using SearXNG. Instead, you get high-quality results like Google or Bing. SearXNG also randomizes the results so no SEO or top-ranking will not gonna work. You can also enable independent search engines like Brave Search, Mojeek etc.

In conclusion, when you ask the same question from multiple sources, it can lead to confusion or something else entirely. What are your thoughts on this? Feel free to share in the comments.

So Ya! That's all for this project. Nothing more to say. I have a good, private, My own search engine with extreme customizations. If you're interested in IT give it a shot, you'll gonna like it and I'm kinda happy with this, at least for the time being.

Thanks for reading, See Ya!
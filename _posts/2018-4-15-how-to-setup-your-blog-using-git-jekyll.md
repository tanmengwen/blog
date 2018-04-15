---
layout: post
title:  "How to setup your blog using github and jekyll"
subtitle: "A very fast and easy way to build your blog website"
date:   2018-4-14 10:45:13 -0400
tag: jekyll
#background: '/img/posts/04.jpg'
---

## References

- [*GitHub Pages*](https://pages.github.com/)
- [*jekyll*](https://jekyllrb.com/)

## Steps
 
### 1. Create a git repository using your github account

- For example, my new github repository name is [*tanmengwen.github.io*](https://github.com/tanmengwen/tanmengwen.github.io).

### 2. Install jekyll

- [*https://jekyllrb.com/docs/installation/*](https://jekyllrb.com/docs/installation/)

### 3. Find a blog website template on [*Start Bootstrap*](https://startbootstrap.com/)

- My blog's template is [*Clean blog*](https://startbootstrap.com/template-overviews/clean-blog/)
- Download the template file.

### 4. Start jekyll serve and review Clean blog website


```
$ cd ~/startbootstrap-clean-blog-jekyll
$ bundle exec jekyll serve
```

Using the browser open the server address: http://127.0.0.1:4000/

### 5. Deploy your site using GitHub for free

```
$ cd ~/startbootstrap-clean-blog-jekyll
$ git init
$ git add .
$ git commit -am "first commit"
$ git remote add origin https://github.com/tanmengwen/tanmengwen.github.io.git
$ git push
```

You will see Clean blog files in your github repository.

Done!!! Blog address is [*https://tanmengwen.github.io/*](https://github.com/tanmengwen/tanmengwen.github.io).


If you still have some questions, there is a good video [*How to Install Jekyll Themes*](https://www.youtube.com/watch?v=bty7LHm14CA&index=13&list=PLm_Qt4aKpfKijgP0rDH7FSJOlS9IBGbT1) which shows how to setup the blog step by step.


<video id="video" width="640" height="360" controls="controls" preload="none" poster="/blog/assets/images/posts/video-bg1.png">
      <source id="mp4" src="https://r5---sn-npoe7n7z.googlevideo.com/videoplayback?lmt=1472114448381103&signature=1F63C077E9A621CB4779E2F80B1D75579702B86E.55C3DD4EA3360497C0049F2BDA27837CF2B353B3&id=o-AN1xHFwG8LHitnhgAdXlodfDL2eCE5oh7vhWgVTpY4zs&fvip=9&sparams=dur,ei,expire,id,initcwndbps,ip,ipbits,itag,lmt,mime,mip,mm,mn,ms,mv,pl,ratebypass,requiressl,source&ipbits=0&ei=NOHSWoa3EdHdgAfktLSQBQ&ip=79.120.177.106&mime=video%2Fmp4&itag=22&key=cms1&pl=24&requiressl=yes&dur=625.220&ratebypass=yes&expire=1523791252&c=WEB&source=youtube&video_id=bty7LHm14CA&title=How+to+Install+Jekyll+Themes+-+Tutorial+12&rm=sn-po8puxa-c0qs76&req_id=24c3f8911caea3ee&redirect_counter=2&cm2rm=sn-c0qey7s&cms_redirect=yes&mip=164.52.34.60&mm=34&mn=sn-npoe7n7z&ms=ltu&mt=1523769621&mv=m" type="video/webm">
      <!-- <source id="webm" src="https://r5---sn-npoe7n7z.googlevideo.com/videoplayback?lmt=1472114448381103&signature=1F63C077E9A621CB4779E2F80B1D75579702B86E.55C3DD4EA3360497C0049F2BDA27837CF2B353B3&id=o-AN1xHFwG8LHitnhgAdXlodfDL2eCE5oh7vhWgVTpY4zs&fvip=9&sparams=dur,ei,expire,id,initcwndbps,ip,ipbits,itag,lmt,mime,mip,mm,mn,ms,mv,pl,ratebypass,requiressl,source&ipbits=0&ei=NOHSWoa3EdHdgAfktLSQBQ&ip=79.120.177.106&mime=video%2Fmp4&itag=22&key=cms1&pl=24&requiressl=yes&dur=625.220&ratebypass=yes&expire=1523791252&c=WEB&source=youtube&video_id=bty7LHm14CA&title=How+to+Install+Jekyll+Themes+-+Tutorial+12&rm=sn-po8puxa-c0qs76&req_id=24c3f8911caea3ee&redirect_counter=2&cm2rm=sn-c0qey7s&cms_redirect=yes&mip=164.52.34.60&mm=34&mn=sn-npoe7n7z&ms=ltu&mt=1523769621&mv=m" type="video/webm">
      <source id="ogv" src="https://r5---sn-npoe7n7z.googlevideo.com/videoplayback?lmt=1472114448381103&signature=1F63C077E9A621CB4779E2F80B1D75579702B86E.55C3DD4EA3360497C0049F2BDA27837CF2B353B3&id=o-AN1xHFwG8LHitnhgAdXlodfDL2eCE5oh7vhWgVTpY4zs&fvip=9&sparams=dur,ei,expire,id,initcwndbps,ip,ipbits,itag,lmt,mime,mip,mm,mn,ms,mv,pl,ratebypass,requiressl,source&ipbits=0&ei=NOHSWoa3EdHdgAfktLSQBQ&ip=79.120.177.106&mime=video%2Fmp4&itag=22&key=cms1&pl=24&requiressl=yes&dur=625.220&ratebypass=yes&expire=1523791252&c=WEB&source=youtube&video_id=bty7LHm14CA&title=How+to+Install+Jekyll+Themes+-+Tutorial+12&rm=sn-po8puxa-c0qs76&req_id=24c3f8911caea3ee&redirect_counter=2&cm2rm=sn-c0qey7s&cms_redirect=yes&mip=164.52.34.60&mm=34&mn=sn-npoe7n7z&ms=ltu&mt=1523769621&mv=m" type="video/ogg"> -->

      <p>Your user agent does not support the HTML5 Video element.</p>
</video>


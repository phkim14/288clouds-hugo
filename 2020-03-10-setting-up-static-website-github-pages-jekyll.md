# Setting Up a Static Website with GitHub Pages and Jekyll



The first problem I encountered when I decided to start a blog was deciding which hosting platform I should use. 

There are many great options out there, and I wanted to find a platform that won't break the bank but is still easy to use and customize. After doing a lot of research and considering various hosting platforms (i.e. WordPress, GoDaddy, Squarespace, Bluehost, AWS, Firebase, etc.), I decided on GitHub Pages and Jekyll for the following reasons:

1. It's free.
2. It's "easy" to create.
3. I have no need for dynamic content (at least for now).

I'm enjoying it so far, but I would not recommend this to someone with no experience with web development or coding in general. GitHub Pages does not provide a GUI like WordPress or Squarespace where coding experience is not needed. Especially if you want to customize the theme and look of your website, you have to code it yourself. Even just writing a blog post, you have to write the blog in a markdown file. If you are not familiar with that format, this can be very hindering to your blogging process, which should primarily focus on creating the content, not on putting the content out.  

If you do have basic familiarity with web-development and git and you just need a simple static website, this can be a good option for you. So here's how I did it... (please note that I'm using a Mac.)

## Process Overview
1. Install Jekyll.
2. Create a local git repository.
3. Start a Jekyll project.
4. Customize the website. 
5. Configure a custom domain. 
6. Configure the website with SEO plugins.
7. Create a new repository on GitHub.
8. Connect the local repository with the remote GitHub repository.
9. Publish the site via GitHub Pages.  

## Details and Resources
### Install Jekyll
1. Use [monfresh/laptop][monfresh-link] shell script to download packages (Ruby, Bundler, Homebrew, etc.) needed to install Jekyll. 
Note that this shell is for Macs only. 
2. Open Terminal and type `gem install jekyll` to install Jekyll.

### Create a Local Repository and Start a Jekyll Project
Do this using the Terminal. 
1. Create a directory for the project files. Hint: `mkdir <directory name>`
2. Go into the new directory. 
3. Type `git init` to create a local git repository.
4. Type `jekyll new <project-name>` to create a new project.
5. Add `.DS_Store` to the `.gitignore` file.

### Customize the Website
I used [Bill Raymond's YouTube videos][billraymond-link] on how to set up GitHub Pages as a reference to customize my website. 
He also shows you how you can preview your website locally.
* From the Terminal, type `bundle update` to create the bundle the first time.
* Every time you want to preview the website, type `bundle exec jekyll serve`, and once it's built successfully, you'll see the IP address in the Terminal where you can view the site. Note that the preview does not update dynamically as you change your code.

I will make additional blog posts with details on how you can customize your GitHub Pages + Jekyll website.

Visit [my GitHub repository][mygithubrepo-link] of the website when it used to be in jekyll to view my files.

### Configure a Custom Domain
I bought my domain through Google Domains then created custom DNS records in Google Domains.

Here are the IP addresses you need to add to the custom DNS record: 
* 185.199.108.153
* 185.199.109.153
* 185.199.110.153
* 185.199.111.153

### Configure SEO plugins
I did the following activities to optimize my site's searchability: 
* Set up Google Analytics - add the line `google_analytics: <tracking ID>` in `_config.yml` file.
* Set up Google Search Console.
* Add a sitemap to Google Search Console - generate a site map by adding the line `- jekyll-sitemap` under `plugins:` in `_config.yml` file (refer to this [blogpost][jekyll-sitemap-link]).
* Add metadata tags by using [jekyll-seo-tag][jekyll-seo-tag-link].

### Publish via GitHub Pages
1. Log into [github.com][github-link].
2. Create a new repository without a README.md file.
3. Open Terminal and go to the directory with your website files.
4. Type `git remote add origin <new repository link>`.
5. Type `git push -u origin master`. (This assumes that you have made at least one commit in your locla repository.)
6. Go back to [github.com][github-link] and to your website repository.
7. Click "Settings" and under "GitHub Pages" section, choose "master branch" as your Source and type in your custom domain name under "Custom domain." 

Your site is now published!


[monfresh-link]: https://github.com/monfresh/laptop
[billraymond-link]: https://www.youtube.com/user/ProjectNationBill
[mygithubrepo-link]: https://github.com/phkim14/288clouds-old-jekyll
[github-link]: https://github.com
[jekyll-sitemap-link]: https://blog.webjeda.com/jekyll-sitemap/
[jekyll-seo-tag-link]: https://github.com/jekyll/jekyll-seo-tag


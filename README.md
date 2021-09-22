# IFT6758 Blog Post Template

This blog post template is based off of the [Lanyon](https://github.com/poole/lanyon) template (the original readme can be found at the bottom).



## Installation

### Ubuntu

1. Install Ruby (I'm using v3.2.5)

```bash
sudo apt install ruby-full
```

2. Once installed, add the following to your `.bashrc` or whatever terminal startup script you may use:

```bash
export GEM_HOME="$HOME/.gem"
export PATH="$HOME/.gem/bin:$PATH"
```

3. Install bundler:

```bash
gem install jekyll bundler
```

4. Install dependencies:

From within this repository's root directory, run:

```
bundle install
```

### MacOS and Windows

Mac and Windows users can find relevant guides for installing Jekyll here:

- [Windows guide](https://jekyllrb.com/docs/installation/windows/)
- [MacOS guide](https://jekyllrb.com/docs/installation/macos/)

Additionally, you will need to do a few steps in Windows before serving you are able to serve your page locally. The steps below are Windows only, and you won't need them if you're on a Linux based machine. 

### Windows Specific Steps:

Before running these, you'd want to make sure your Ruby and Jekyll installations are functional.

1. From within the repository's root directory, run: 

```
bundle add webrick
```

2. Open the `gemfile` present in the root directory of the repository, and add the following line at the end of the file: 

```
gem 'wdm', '>= 0.1.0'
```

3. Run the following command in the same directory: 

```
gem install wdm
```

You should be able to serve the page locally on Windows after these steps. 

## Serving the Page Locally

Once you've installed jekyll and all of the dependencies, you can now serve the webpage on your local machine for development purposes using the `jekyll serve` command, using bundle to handle any dependencies.
In your terminal, from the directory containing the Jekyll project run:

```bash
bundle exec jekyll serve
```

You should see something along the lines of:

```
> bundle exec jekyll serve
Configuration file: /home/USER/ift6758-blog-template/_config.yml
            Source: /home/USER/ift6758-blog-template
       Destination: /home/USER/ift6758-blog-template/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 0.661 seconds.
 Auto-regeneration: enabled for '/home/USER/ift6758-blog-template'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

If you see this, you've successfully served your web page locally!
You can access it at server address specified, in this case `http://127.0.0.1:4000/`.

## Configuration

You will find some basic configurations that you will need to modify in the [`_config.yml`](./_config.yml) file found in the repo's root directory.
You should modify the title, tagline, and description to something more appropriate, as well as update the author information.
Be careful with the urls however, as this can break things if you're not careful!

If you'd like to make your blog posts wider (i.e. have the content span a wider area of the page), you can modify this setting on line 85 of [`public/css/lanyon.css`](./public/css/lanyon.css).
Feel free to experiment with sizes that you prefer.

## Creating Content

All posts are defined as markdown files in the [`_posts/`](./_posts/) directory of this repository, and they must follow the naming convention of `YYYY-MM-DD-postname.md`.
The date is automatically parsed from the filename and embedded into the post.
Checkout the post found in `./_posts/2021-09-17-ift6758-demo.md` for an example which embeds interactive HTML figures generated with [Plotly](https://plotly.com/python/interactive-html-export/).

As submission for the milestones, you're expected to create a single blogpost entry containing all of the answers and figures for all of the questions.
Create a file `./_posts/YYYY-MM-DD-milestone-1.md` and use a layout like the following:

```md
---
layout: post
title: Milestone 1
---

## Question 1

...content...

## Question 2

...content...
```

Make sure that its obvious where each answer to the questions are!

<br>
<br>
<br>

___
*The following content is the original Lanyon Readme file.*
___
<br>
<br>
<br>

# Lanyon

Lanyon is an unassuming [Jekyll](http://jekyllrb.com) theme that places content first by tucking away navigation in a hidden drawer. It's based on [Poole](http://getpoole.com), the Jekyll butler.

![Lanyon](https://f.cloud.github.com/assets/98681/1825266/be03f014-71b0-11e3-9539-876e61530e24.png)
![Lanyon with open sidebar](https://f.cloud.github.com/assets/98681/1825267/be04a914-71b0-11e3-966f-8afe9894c729.png)


## Contents

- [Usage](#usage)
- [Options](#options)
  - [Sidebar menu](#sidebar-menu)
  - [Themes](#themes)
  - [Reverse layout](#reverse-layout)
- [Development](#development)
- [Author](#author)
- [License](#license)


## Usage

Lanyon is a theme built on top of [Poole](https://github.com/poole/poole), which provides a fully furnished Jekyll setup—just download and start the Jekyll server. See [the Poole usage guidelines](https://github.com/poole/poole#usage) for how to install and use Jekyll.


## Options

Lanyon includes some customizable options, typically applied via classes on the `<body>` element.


### Sidebar menu

Create a list of nav links in the sidebar by assigning each Jekyll page the correct layout in the page's [front-matter](http://jekyllrb.com/docs/frontmatter/).

```
---
layout: page
title: About
---
```

**Why require a specific layout?** Jekyll will return *all* pages, including the `atom.xml`, and with an alphabetical sort order. To ensure the first link is *Home*, we exclude the `index.html` page from this list by specifying the `page` layout.


### Themes

Lanyon ships with eight optional themes based on the [base16 color scheme](https://github.com/chriskempson/base16). Apply a theme to change the color scheme (mostly applies to sidebar and links).

![Lanyon with red theme](https://f.cloud.github.com/assets/98681/1825270/be065110-71b0-11e3-9ed8-9b8de753a4af.png)
![Lanyon with red theme and open sidebar](https://f.cloud.github.com/assets/98681/1825269/be05ec20-71b0-11e3-91ea-a9138ef07186.png)

There are eight themes available at this time.

![Available theme classes](https://f.cloud.github.com/assets/98681/1817044/e5b0ec06-6f68-11e3-83d7-acd1942797a1.png)

To use a theme, add any one of the available theme classes to the `<body>` element in the `default.html` layout, like so:

```html
<body class="theme-base-08">
  ...
</body>
```

To create your own theme, look to the Themes section of [included CSS file](https://github.com/poole/lanyon/blob/master/public/css/lanyon.css). Copy any existing theme (they're only a few lines of CSS), rename it, and change the provided colors.


### Reverse layout

![Lanyon with reverse layout](https://f.cloud.github.com/assets/98681/1825265/be03f2e4-71b0-11e3-89f1-360705524495.png)
![Lanyon with reverse layout and open sidebar](https://f.cloud.github.com/assets/98681/1825268/be056174-71b0-11e3-88c8-5055bca4307f.png)

Reverse the page orientation with a single class.

```html
<body class="layout-reverse">
  ...
</body>
```


### Sidebar overlay instead of push

Make the sidebar overlap the viewport content with a single class:

```html
<body class="sidebar-overlay">
  ...
</body>
```

This will keep the content stationary and slide in the sidebar over the side content. It also adds a `box-shadow` based outline to the toggle for contrast against backgrounds, as well as a `box-shadow` on the sidebar for depth.

It's also available for a reversed layout when you add both classes:

```html
<body class="layout-reverse sidebar-overlay">
  ...
</body>
```

### Sidebar open on page load

Show an open sidebar on page load by modifying the `<input>` tag within the `sidebar.html` layout to add the `checked` boolean attribute:

```html
<input type="checkbox" class="sidebar-checkbox" id="sidebar-checkbox" checked>
```

Using Liquid you can also conditionally show the sidebar open on a per-page basis. For example, here's how you could have it open on the homepage only:

```html
<input type="checkbox" class="sidebar-checkbox" id="sidebar-checkbox" {% if page.title =="Home" %}checked{% endif %}>
```

## Development

Lanyon has two branches, but only one is used for active development.

- `master` for development.  **All pull requests should be to submitted against `master`.**
- `gh-pages` for our hosted site, which includes our analytics tracking code. **Please avoid using this branch.**


## Author

**Mark Otto**
- <https://github.com/mdo>
- <https://twitter.com/mdo>


## License

Open sourced under the [MIT license](LICENSE.md).

<3

# cllei12.github.io

[Website for Characterizing Online Attitudes](https://cllei12.github.io/)

You can edit [index.md](index.md) to maintain and preview the content for the website.

### Tips

[Basic writing and formatting syntax of Markdown](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

[Typora](https://typora.io/) - an excellent markdown editor

### new template 

[hyde](https://github.com/poole/hyde)

Usage: 
- install brew for MacOS
- install gem 
    - https://jekyllrb.com/docs/installation/

    ```shell
    <!-- MacOS, intall chruby and ruby-install -->
    brew install chruby ruby-install
    ruby-install ruby

    <!-- check ruby version -->
    chruby
    >> ruby-3.1.2

    <!-- configure your shell to automatically use chruby -->
    echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
    echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
    echo "chruby ruby-3.1.2" >> ~/.zshrc

    <!-- Quit and relaunch Terminal, then check that everything is working: -->
    ruby -v
    ```
- Install dependencies:
    - https://github.com/poole/poole#usage
    ```shell
    gem install jekyll jekyll-gist jekyll-sitemap jekyll-seo-tag jekyll-paginate
    minima
    github-pages
    ```

https://jekyllrb.com/docs/installation/macos/

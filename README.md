# Steam Scraper Plus

This repository contains [Scrapy](https://github.com/scrapy/scrapy) spiders for **crawling products** and **scraping all user-submitted reviews** from the [Steam game store](https://steampowered.com).
A few scripts for more easily managing and deploying the spiders are included as well.

This repository forks and extends the code accompanying the *Scraping the Steam Game Store* article published on the [Scrapinghub blog](https://blog.scrapinghub.com/2017/07/07/scraping-the-steam-game-store-with-scrapy/) and the [Intoli blog](https://intoli.com/blog/steam-scraper/).

The focus of this scraper is to fetch game product page metadata compatible with the latest layout of Steam.

Compared to the original crawler, this version contains:
* Fixes:
  - Developer, publisher, release date fields populate correctly
  - Developer and publisher fields support lists of values in each field
  - Using the steamid parameter in the product crawler to fetch just one product page by ID now works correctly

* Additions:
  - Fields added:
    - short_description
    - long_description
    - cover_image_url
    - game_image_url 
  - Parameters added:
    - In product crawler:
      - url_file

* Minor enhancements:
  - Resolved deprecation warnings for scrapy import on the Product spider
  - Gitignore file extended to ignore various temporary files

## Installation

After cloning the repository with
```bash
git clone git@github.com:prncc/steam-scraper.git
```
start and activate a Python 3.6+ virtualenv with
```bash
cd steam-scraper
virtualenv -p python3.6 env
. env/bin/activate
```
Install Python requirements via:
```bash
pip install -r requirements.txt
```

By the way, on macOS you can install Python 3.6 via [homebrew](https://brew.sh):
 ```bash
 brew install python3
```
On Ubuntu you can use [instructions posted on askubuntu.com](https://askubuntu.com/questions/865554/how-do-i-install-python-3-6-using-apt-get).

## Crawling the Products

The purpose of `ProductSpider` is to discover product pages on the [Steam product listing](http://store.steampowered.com/search/?sort_by=Released_DESC) and extract useful metadata from them.
A neat feature of this spider is that it automatically navigates through Steam's age verification checkpoints.

### Crawling all products on Steam
You can initiate the multi-hour crawl with
```bash
scrapy crawl products -o output/products_all.jl --logfile=output/products_all.log --loglevel=INFO -s JOBDIR=output/products_all_job -s HTTPCACHE_ENABLED=False
```
When it completes you should have metadata for all games on Steam in `output/products_all.jl`.

### Crawling a batch of products on Steam based on a URL list
You can create a plain text file with a list of Steam product URLs (one by line) and crawl those pages specifically.

In the sample below, the file is called url_file.txt and is stored in the script's root folder. Prior to running this command you will need to create the "output" folder in the same location as well. In this sample, as JOBDIR is specified, the crawler can be resumed. Remove the latter parameter to disable resuming and always crawl all URLs on each run.
```bash
scrapy crawl products -a url_file=url_file.txt -o output/products_specific.jl --logfile=output/products_specific.log --loglevel=INFO -s JOBDIR=output/products_specific
```
When it completes you should have metadata for all games in your URL file in `output/products_specific.jl`.

### Crawling a single product on Steam based on its AppID
You can pass the Steam AppID for a product directly to crawl its page specifically.
```bash
scrapy crawl products -a steam_id=404200 -o output/products_specific.jl --logfile=output/products_specific.log --loglevel=INFO
```
When it completes you should have metadata for the game you specified in `output/products_specific.jl`.

### Sample output
Here's some sample output:
```python
{
    "url": "https://store.steampowered.com/app/404200/",
    "reviews_url": "http://steamcommunity.com/app/404200/reviews/?browsefilter=mostrecent&p=1",
    "id": "404200",
    "title": "WARTILE",
    "genres": ["RPG", "Strategy"],
    "developer": ["Playwood Project"],
    "publisher": ["Deck13", "WhisperGames"],
    "release_date": "8 Feb, 2018",
    "app_name": "WARTILE",
    "tags": ["RPG", "Strategy", "Board Game", "Action", "Strategy RPG", "Action RPG", "Tactical RPG", "Medieval", "Hex Grid", "Adventure", "Isometric", "Tactical", "Deckbuilding", "Tabletop", "Singleplayer", "Real Time Tactics", "Real-Time", "Beautiful", "3D", "Atmospheric"],
    "price": "62.00 AED",
    "sentiment": "Mostly Positive",
    "metascore": 68,
    "early_access": false,
    "short_description": "Wartile is a cool-down based strategy game in which you control a warband of Viking figurines in a miniature universe inspired by Norse mythology. Unravel its secrets and rein the powers of the gods.",
    "long_description": ["<div id=\"game_area_description\" class=\"game_area_description\">\r\n\t\t\t\t\t\t\t<h2>About This Game</h2>\r\n\t\t\t\t\t\t\t<strong>A miniature world coming to life</strong><br>Experience a living, breathing tabletop video game that invites the player into a miniature universe full of small adventures set in beautifully handcrafted diorama battle boards inspired by Norse mythology to honor the Vikings!<br><img src=\"https://cdn.cloudflare.steamstatic.com/steam/apps/404200/extras/Apng_Battleboard_Stills.png?t=1637857199\"><br><br><strong>Cool-down based combat that keeps the action flowing</strong><br>Wartile is a cool-down based game that keeps the action flowing, with ample opportunities to plan your moves. Although it contains the strategic elements from turn-based games, a mixture of slow down features and cool-down based gameplay maintains the tension of battle while allowing for breathing room to make tactical decisions. At its heart, Wartile is a game about positioning and tactical decision making.<br><img src=\"https://cdn.cloudflare.steamstatic.com/steam/apps/404200/extras/Apng_Combat_Showcase_1.png?t=1637857199\"><br><br><strong>Your control the pace of battle</strong><br>With Slow Time always available you can control the speed of the fight giving you an advantageous tactical benefit in critical situations where every action counts. <br><img src=\"https://cdn.cloudflare.steamstatic.com/steam/apps/404200/extras/Apng_SlowTime_v1.png?t=1637857199\"><br><br><strong>Collect &amp; level up figurines and customize their equipment and abilities</strong><br>Collect and level up an array of different figurines. Customize your Warband with armor pieces, weapons, unique combat abilities and set up your deck of Battle Cards, to provide a choice of tactical options before they embark on each quest.<br><img src=\"https://cdn.cloudflare.steamstatic.com/steam/apps/404200/extras/Apng_Customize_V2.png?t=1637857199\">\t\t\t\t\t\t</div>"],
    "cover_image_url": "https://cdn.akamai.steamstatic.com/steam/apps/404200/header.jpg?t=1637857199",
    "game_image_url": "https://cdn.akamai.steamstatic.com/steam/apps/404200/ss_8b05c8bef08af6edf3a4de3b3a7431d85e4e3fb6.jpg"
}
```

## Extracting the Reviews

The purpose of `ReviewSpider` is to scrape all user-submitted reviews of a particular product from the [Steam community portal](http://steamcommunity.com/). 
By default, it starts from URLs listed in its `test_urls` parameter:
```python
class ReviewSpider(scrapy.Spider):
    name = 'reviews'
    test_urls = [
        "http://steamcommunity.com/app/316790/reviews/?browsefilter=mostrecent&p=1",  # Grim Fandango
        "http://steamcommunity.com/app/207610/reviews/?browsefilter=mostrecent&p=1",  # The Walking Dead
        "http://steamcommunity.com/app/414700/reviews/?browsefilter=mostrecent&p=1"   # Outlast 2
    ]
```
It can alternatively ingest a text file containing URLs such as
```
http://steamcommunity.com/app/316790/reviews/?browsefilter=mostrecent&p=1
http://steamcommunity.com/app/207610/reviews/?browsefilter=mostrecent&p=1
http://steamcommunity.com/app/414700/reviews/?browsefilter=mostrecent&p=1
```
via the `url_file` command line argument:
```bash
scrapy crawl reviews -o reviews.jl -a url_file=url_file.txt -s JOBDIR=output/reviews
```
An output sample:
```python
{
  'date': '2017-06-04',
  'early_access': False,
  'found_funny': 5,
  'found_helpful': 0,
  'found_unhelpful': 1,
  'hours': 9.8,
  'page': 3,
  'page_order': 7,
  'product_id': '414700',
  'products': 179,
  'recommended': True,
  'text': '3 spooky 5 me',
  'user_id': '76561198116659822',
  'username': 'Fowler'
}
```

If you want to get all the reviews for all products, `split_review_urls.py` will remove duplicate entries from `products_all.jl` and shuffle `review_url`s into several text files.
This provides a convenient way to split up your crawl into manageable pieces.
The whole job takes a few days with Steam's generous rate limits.

## Deploying to a Remote Server

This section briefly explains how to run the crawl on one or more t1.micro AWS instances.

First, create an Ubuntu 16.04 t1.micro instance and name it `scrapy-runner-01` in your `~/.ssh/config` file:
```
Host scrapy-runner-01
     User ubuntu
     HostName <server's IP>
     IdentityFile ~/.ssh/id_rsa
```
A hostname of this form is expected by the `scrapydee.sh` helper script included in this repository.
Make sure you can connect with `ssh scrappy-runner-01`.

### Remote Server Setup

The tool that will actually run the crawl is [scrapyd](http://scrapyd.readthedocs.io/en/stable/) running on the remote server.
To set things up first install Python 3.6:
```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt update
sudo apt install python3.6 python3.6-dev virtualenv python-pip
```
Then, install scrapyd and the remaining requirements in a dedicated `run` directory on the remote server: 
```bash
mkdir run && cd run
virtualenv -p python3.6 env
. env/bin/activate
pip install scrapy scrapyd botocore smart_getenv  
```
You can run `scrapyd` from the virtual environment with
```bash
scrapyd --logfile /home/ubuntu/run/scrapyd.log &
```
You may wish to use something like [screen](https://www.gnu.org/software/screen/) to keep the process alive if you disconnect from the server.

### Controlling the Job

You can issue commands to the scrapyd process running on the remote machine using a simple [HTTP JSON API](http://scrapyd.readthedocs.io/en/stable/index.html).
First, create an egg for this project:
```bash
python setup.py bdist_egg
```
Copy the egg and your review url file to `scrapy-runner-01` via
```bash
scp output/review_urls_01.txt scrapy-runner-01:/home/ubuntu/run/
scp dist/steam_scraper-1.0-py3.6.egg scrapy-runner-01:/home/ubuntu/run
```
and add it to scrapyd's job directory via 
```bash
ssh -f scrapy-runner-01 'cd /home/ubuntu/run && curl http://localhost:6800/addversion.json -F project=steam -F egg=@steam_scraper-1.0-py3.6.egg'
```
Opening port 6800 to TCP traffic coming from your home IP would allow you to issue this command without going through SSH.
If this command doesn't work, you may need to edit `scrapyd.conf` to contain
```
bind_address = 0.0.0.0
```
in the `[scrapyd]` section.
This is a good time to mention that there exists a [scrapyd-client](https://github.com/scrapy/scrapyd-client) project for deploying eggs to scrapyd equipped servers.
I chose not to use it because it doesn't know about servers already set up in `~/.ssh/config` and so requires repetitive configuration.

Finally, start the job with something like
```bash
ssh scrapy-runner-01 'curl http://localhost:6800/schedule.json -d project=steam -d spider=reviews -d url_file="/home/ubuntu/run/review_urls_01.txt" -d jobid=part_01 -d setting=FEED_URI="s3://'$STEAM_S3_BUCKET'/%(name)s/part_01/%(time)s.jl" -d setting=AWS_ACCESS_KEY_ID='$AWS_ACCESS_KEY_ID' -d setting=AWS_SECRET_ACCESS_KEY='$AWS_SECRET_ACCESS_KEY' -d setting=LOG_LEVEL=INFO'
```
This command assumes you have set up an S3 bucket and the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables.
It should be pretty easy to customize it for non-S3 output, however.

The `scrapydee.sh` helper script included in the `scripts` directory of this repository has some shortcuts for issuing commands to scrapyd-equipped servers with hostnames of the form `scrapy-runner-01`.
For example, the command
```bash
./scripts/scrapydee.sh status 1
# Executing status()...
# On server(s): 1.
```
will run the `status()` function defined in `scrapydee.sh` on `scrapy-runner-01`.
See that file for more command examples.
You can also run each of the included commands on multiple servers:
First, change the `all()` function within `scrapydee.sh` to match the number of servers you have configured.
Then, issue a command such as
```bash
./scripts/scrapydee.sh status all
```
The output is a bit messy, but it's a quick and easy way to run this job.

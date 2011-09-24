---
layout: post
title: "Image Scraping Example Using the Ruby Mechanize Library"
date: 2011-09-24 09:30
comments: true
categories: ruby
---

Yesterday, on Hacker News, sadiq posted a link to [this amazing forum thread on
conceptart.org]("http://www.conceptart.org/forums/showthread.php?t=870").  You
can also read the Hacker News thread
[here]("http://news.ycombinator.com/item?id=3031684").

The server seemed to be under a lot of load, so I decided it would be faster
to write a script that would scrape the images for me. Per jimmyjim's request,
here is the script itself, along with a few comments to make it more readable:

```ruby
require 'mechanize'

BASE_URI = "http://www.conceptart.org/forums/showthread.php?t=870"
CSS_SELECTOR = "td.alt1 > div > img"
SAVE_DIR = "images/"

class Mechanize
    # This adds a safe_get method to the Mechanize agent which tries
    # to handle network timeouts and similar intermittent errors by
    # retrying the get up to 10 times.
    def safe_get(url)
        errors = 0
        begin
            return self.get(url)
        rescue => e
            errors += 1
            if errors > 10
                raise e
            end
            retry
        end
    end
end

def save_images(page_num)
    agent = Mechanize.new
    url = "#{BASE_URI}&page=#{page_num}"
    page = agent.safe_get(url)
    # Mechanize uses Nokogiri to the parse the HTML, so you can use CSS
    # selectors to search for elements in the response.
    images = page.search(CSS_SELECTOR)
    img_num = 1
    images.each do |img|
        path = img.attributes['src'].value
        # By checking for attachmentid in the path, the script skips over
        # icons (mostly smilies) caught by the CSS selector above.
        if !path.include?('attachmentid')
            next
        end
        agent.safe_get(path).save_as(
            "#{SAVE_DIR}/page_#{page_num}_img_#{img_num}.jpg")
        img_num += 1
    end
end

threads = []
# The forum thread had about 70 pages, so here I'm just erring on the safe
# side and looking at pages 1 through 75.
1.upto(75).each do |page_num|
    threads << Thread.new { save_images(page_num) }
end
threads.each do |t|
    # This waits for the thread to finish and prints its return value, which
    # should be 0.  If the thread raised the same error more than 10 times in a
    # row, it will be raised here and you'll see what the error was.
    p t.value
end
```

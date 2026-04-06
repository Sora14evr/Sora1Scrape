# Sora1Scrape
A suite of utilities for grabbing the last of your Sora 1 creations before it's too late!

In March, OpenAI announced the sunset of their Sora 1 platform.  At that point in time I had amassed 10's of thousands of generations and was NOT very good about downloading while I was creating and had a big problem to solve in a short amount of time.

OpenAI did not help us out a whole lot, promising a data export tool, which never happened, as well as a notification of the time window in which our creations would still be available, which also never happened.

About a week after the announcement, the entire site switched over to the Sora 2 interface, and at a first look, it seemed all access to the Sora 1 creations were gone without a trace.

I turned to ChatGPT and began on a journey to hack something together that might make my job a little easier.

Scrolling down the My Media page with developer tools, missing links left and right as I attempted to work through the API and scrape Fetch, cURL, HAR exports.

Furious with ChatGPT for leading me down dead ends for several days, I decided to go the ugly route:  simulating mouse clicks and keystrokes.  It wasn't pretty, but it worked reasonably well.

In its current state, the scraping tool takes a list of Sora 1 Task URLS and navigates through them in a web browser, downloading appropriately, then takes inventory of the files successfully downloaded to confirm 4 Loop videos, 2 New Videos, or 2 Images, prompting for a retry when an error is found.

Right now, there are a lot of limitations.  The script currently does not "know" what the browser is doing as it doesn't communicate through the API, and therefore has no sense of what layout is used.  The mouse coordinates used in the script were decided around the rectangular 4-loop video formation.  Currently not all layouts are supported, and there may be some that need to be skipped and addressed manually, such as the square 4-loop layout.

GETTING STARTED:
Before we get into running the script, there is a little bit of legwork that must be done manually:
  1) Find a valid task link in your browser history or cache, in the form https://sora.chatgpt.com/t/task_01kxxxxxxxxxxxxxxxxxxxx.  Open the link in the web browser.  This will allow you to escape the seemingly forced Sora 2 interface and give you access to your Sora 1 creations.

  2) I've found the best way to capture all links is through the Alerts menu.  Click the bell icon and scroll down as far as you want to capture. (not too fast, you want to make sure all of the thumbnails load before scrolling further). Right-click anywhere in the window and select "Inspect Element".

  3) Find ```<div class="flex flex-col">``` and below are all the links to your creation tasks. Right click ```<div class="flex flex-col">``` and select Copy Element.

Copy and paste to a text editor and save it as raw_scrape.txt. The links to your creations are now safe and sound from the evil clutches of OpenAI and you won't need to crawl through the alerts menu anymore. You can now run scripts.

The script is written in Python, and if you don't already have the Python interpreter you will need to download and install the appropriate package for your system from python.org.

SCRAPE.BAT
----------
This is a clickable launcher that runs the scraping script through the Python interpreter. It can be executed in a window or at a command prompt.

The first thing the script will do is look at raw_scrape.txt which is full of useless garbage in poorly-formatted HTML.

To get to the links, the script needs to process the file.  First, it uses the Python model beautifulsoup4 to properly format the HTML.

Next, it isolates the URLs from the string appended with a %2F, keeping it from being recognized as a hyperlink.  The script finds all instances of %2F and replaces them with a space.

Now the script strips out the URLs only.

Finally, the script does a find and replace for "videos.openai.com/az/vg-assets" and changes all instances to "sora.chatgpt.com/t" and blows away all of the URLs containing "thumb.webp" and "w3.org".

We now have a list of tasks to be scraped through.

At this point, the script will open the first link in a browser,  click the first panel of the task, press D then click the download button, then press the "back" button to return to the task, rinse and repeat. (The download directory should be set to the same folder containing the scripts).

After the process has completed, the script will check the files in the current directory, making sure that files containing "Loop Video" are in quantities of 4, "New Videos" and "Image" files are in quantities of 2.  If these counts are not met, the script will give you the option to retry, or skip and continue.  Keep in mind, there are a lot of cases that will need to be treated as exceptions, including corrupt downloads, non-standard file counts in the original generations, and downloads that take longer to generate than the typical delay (which can be adjusted by editting scrape.py)

The script is set up to recognize and verify files as follows:
  -Links to tasks are in the format task_01kXYZxxxxxxxxxxx where XY can be considered the "Task Group".  Generally, media files associated with a task link will match these first two characters.  XYZ can be considered a "Media Set", as it will generally be common among files within a task.  

Once the script is completed, you can go back and address any ignored failures manually, or use the scan utility to create a list of failures based on successfully downloaded files.


SCAN.BAT
--------
This is a clickable launcher that runs the file scan routine on all subdirectories of the current working folder.

It uses the same criteria as the built-in task verifier in the scraper script, with the first two characters following the "_01k" designation being regarded as the "Task group" and the first three characters as a "Media Set".


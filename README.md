# crawl_sites

1. <site>/sitemap.xml
    Eg:- www.kishanhs.com/sitemap.xml

2. Every website should have an XML sitemap. They are especially important for:
    Large sites
    New sites
    Sites with lots of orphaned pages
    Sites that use lots of images and videos

 
3. What XML Sitemaps Are and Why You Need to Have One
  A sitemap tells the crawler which files you think are important in your site, and also provides valuable information about    these files: for example, for pages, when the page was last updated, how often the page is changed, and any alternate language  versions of a page.

4. XML Sitemap Minimum Requirements
  For your XML sitemaps to work optimally, you have to meet the standards. An XML sitemap should:
  Contain only canonical URLs with a 200 status code.
  Include up to 200K URLs per sitemap and up to 50K sitemaps per index sitemap.
  Be referenced in the robots.txt.
  BUTF-8 encoded.
  Be compressed in .gz format.
  Be no larger than 50mb or contain 50,000 URLs (whatever you hit first).


5. Example 
    <?xml version="1.0" encoding="UTF-8"?>
    <urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
    xmlns:image="http://www.google.com/schemas/sitemap-image/1.1">
    <url>
    <loc>http://example.com/sample.html</loc>
    <image:image>
    <image:loc>http://example.com/image.jpg</image:loc>
    </image:image>
    <image:image>
    <image:loc>http://example.com/photo.jpg</image:loc>
    </image:image>
    </url>
    </urlset>

6. 
curl -s -H "Accept: application/xml" -H "Content-Type: application/xml" -X GET https://[URL]/sitemap.xml | grep loc | sed s'/\<loc\>//g' | sed s'/\<\/loc\>//g'

curl -s -H "Accept: application/xml" -H "Content-Type: application/xml" -X GET /temp/filename | grep loc | sed s'/\<loc\>//g' | sed s'/\<\/loc\>//g' > /temp/filename
command to remove loc from urls

HIT sitemap URLS obtained to get actual article data -> cat /tmp/filename | while read line; do curl -H "Accept: application/xml" -H "Content-Type: application/xml" -X GET $line | grep "<loc>" >> /tmp/filename; done

7. Use PARSEHUB to get data based on selection to crawl sites. / xmllint --xpath [another approch to get data]
Use awk/sed to add ", or any missing comma at the end to get a proper JSON format.

https://help.parsehub.com/hc/en-us/articles/218187697-Enter-a-list-of-URLs-to-crawl

{
 "urls": [
 "https://www.walmart.ca/en/ip/hp-stream-14-cb110ca-14-inch-laptop-white-intel-celeron-n4000-intel-uhd-600-4gb-ram-64gb-emmc-windows-10-s-4jc81uaabl/6000198793458",
 "https://www.walmart.ca/en/ip/hp-17-by0002ca-173-laptop-natural-silver-and-ash-silver-core-i5-8250u-intel-uhd-graphics-620-8gb-ddr4-1-tb-5400-rpm-sata-windows-10-home-4bq83uaabl/6000198528157",
 "https://www.walmart.ca/en/ip/acer-aspire-3-156-laptop-amd-e2-9000-amd-radeon-r2-graphics-8-gb-ddr4-1-tb-hard-drive-windows-10-home-nxgnvaa019/6000197843008"
 ]
}


8. Also another alternative approch to crawl sites would be using webhose.io
create an account and use their API to get data from the webhose

import webhoseio
import json

webhoseio.config(token="e47b3bb1-c3d5-446c-8f10-3531655f54fe")
// This part will be replaced from webhose API
query_params = {
	"q": "site:<add ur site name here>.com",
	"ts": "1558897602554",
	"sort": "crawled"
    }

output = webhoseio.query("filterWebContent", query_params)

list_artilce_dict = []
count = 0
MerchantId = 0
for i in range(0, output['totalResults']):
    if count > 99:
        output = webhoseio.get_next()
        count = 0
    MerchantId = 130920
    list_artilce_dict.append([output['posts'][count]['title'], output['posts'][count]['text'] , MerchantId ,output['posts'][count]['thread']['site_categories'] , output['posts'][count]['published'] ,output['posts'][count]['author'],output['posts'][count]['url'], output['posts'][count]['thread']['main_image'], output['posts'][count]['thread']['uuid']])
    count = count + 1

with open('filename.json', 'w') as file:
    json.dump(list_artilce_dict, file)

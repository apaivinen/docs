Oletuksena uutisista ei jää filteröitävää jälkeä sharepoint-listalle. 

1. vaihteohto. Luo uutisesta templaten ja templateen asettaa oletusmetatiedon esim. "uutinen"
2. vaihtoehto. Power automatessa REST kautta luetaan PromotedStatus-tietoa

https://www.c-sharpcorner.com/blogs/how-the-sharepoint-works-before-creating-news-post-site-page


Get-kutsu sivustolle. URI: _api/web/lists/getbytitle('Site Pages')/items
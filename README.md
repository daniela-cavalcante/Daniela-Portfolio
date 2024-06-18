# Exploratory Data Analysis (EDA) of the Chinook Database

This exploratory data analysis (EDA) aims to showcase my SQL knowledge by examining various aspects of the Chinook database. The analysis includes examining album and artist details, identifying top genres and customers, analyzing sales trends, and more. Unfortunately, due to connection issues, I could not connect this database to PowerBI for building a dashboard. However, I have managed to perform several analyses and will describe them below.

### Detailed Analyses and Their Real-World Applications

1. **Top 5 Genres by Total Tracks Sold**
   ```sql
   SELECT
       genre.name AS genre_name,
       COUNT(invoiceline.trackid) AS total_tracks_sold
   FROM genre
       INNER JOIN track ON track.genreid = genre.genreid
       INNER JOIN invoiceline ON invoiceline.trackid = track.trackid
   GROUP BY genre_name
   ORDER BY total_tracks_sold DESC
   LIMIT 5;
   ```
   **Goal and Real-World Application:**  
   This query identifies the top 5 genres based on the total number of tracks sold.  
   **Application:** Music stores and streaming services can use this information to highlight popular genres, optimize inventory, and create targeted marketing campaigns for popular music styles.

2. **Top 10 Customers by Total Spend**
   ```sql
   SELECT
       customer.customerId,
       customer.firstname,
       customer.lastname,
       SUM(invoiceline.unitprice * invoiceline.quantity) AS total_spent
   FROM customer
       JOIN invoice ON invoice.customerid = customer.customerid
       JOIN invoiceline ON invoiceline.invoiceid = invoice.invoiceid
   GROUP BY customer.customerId
   ORDER BY total_spent DESC
   LIMIT 10;
   ```
   **Goal and Real-World Application:**  
   This query identifies the top 10 customers who have spent the most money.  
   **Application:** Businesses can use this data to recognize and reward high-spending customers with loyalty programs, exclusive offers, or personalized services to enhance customer retention and satisfaction.

3. **Genres with Most Tracks in Inventory**
   ```sql
   SELECT 
       genre.name AS genre_name,
       COUNT(track.trackid) AS total_tracks
   FROM genre
       JOIN track ON track.genreid = genre.genreid
   GROUP BY genre_name
   ORDER BY total_tracks DESC;
   ```
   **Goal and Real-World Application:**  
   This query identifies which genres have the most tracks in the inventory.  
   **Application:** Inventory managers can use this information to ensure that the supply of tracks aligns with demand, reduce overstocking of less popular genres, and improve the overall efficiency of inventory management.

4. **Top 5 Countries by Total Spend**
   ```sql
   SELECT
       customer.country AS top_5_countries,
       SUM(invoiceline.unitprice * invoiceline.quantity) AS total_spent
   FROM customer
       JOIN invoice ON invoice.customerid = customer.customerid
       JOIN invoiceline ON invoiceline.invoiceid = invoice.invoiceid
   GROUP BY top_5_countries
   ORDER BY total_spent DESC
   LIMIT 5;
   ```
   **Goal and Real-World Application:**  
   This query identifies the top 5 countries by total spend where the customers are located.  
   **Application:** Businesses can tailor their marketing and sales strategies to target high-revenue regions, allocate resources efficiently, and plan international expansions based on spending trends.

5. **Artists with Most Tracks and Total Sales**
   ```sql
   SELECT
       artist.artistid,
       artist.name,
       COUNT(invoiceline.trackid) AS total_tracks,
       SUM(invoiceline.unitprice * invoiceline.quantity) AS total_sales
   FROM artist
       JOIN album ON album.artistid = artist.artistid
       JOIN track ON track.albumid = album.albumid
       LEFT JOIN invoiceline ON invoiceline.trackid = track.trackid
   GROUP BY artist.artistid, artist.name
   ORDER BY total_tracks DESC;
   ```
   **Goal and Real-World Application:**  
   This query identifies the artists with the most tracks and the total sales for these artists' albums.  
   **Application:** Record labels and music platforms can focus on promoting artists with a high number of tracks and sales, optimize promotional efforts, and plan collaborations or new releases to maximize revenue.

6. **Top 5 Tracks by Sales Despite Length**
   ```sql
   SELECT 
       track.trackid,
       track.name AS top_5_tracks,
       artist.name AS artist_name,
       album.title AS album_title,
       genre.name AS genre_name,
       SUM(invoiceline.unitprice * invoiceline.quantity) AS total_sales
   FROM track
       JOIN album ON album.albumid = track.albumid
       JOIN artist ON artist.artistid = album.artistid
       JOIN invoiceline ON invoiceline.trackid = track.trackid
       JOIN genre ON genre.genreid = track.genreid
   GROUP BY track.trackid, track.name
   HAVING AVG(track.milliseconds) > 300000
   ORDER BY total_sales DESC
   LIMIT 5;
   ```
   **Goal and Real-World Application:**  
   This query identifies the top 5 tracks that customers are enjoying despite their length (greater than 5 minutes).  
   **Application:** Music platforms can use this data to promote long-duration tracks that are performing well, create playlists featuring popular long tracks, and understand customer preferences for track length.

7. **Weekday vs. Weekend Sales Analysis**
   ```sql
   SELECT
       CASE
           WHEN DAYOFWEEK(invoice.invoicedate) IN (1, 7) THEN 'Weekend'
           ELSE 'Weekday'
       END AS invoice_day_category,
       COUNT(*) AS total_invoices,
       AVG(total_sales) AS average_sales_amount
   FROM (
       SELECT
           invoiceline.invoiceid,
           SUM(invoiceline.unitprice * invoiceline.quantity) AS total_sales
       FROM
           invoiceline
       GROUP BY
           invoiceline.invoiceid
   ) AS invoice_totals
       JOIN invoice ON invoice_totals.invoiceid = invoice.invoiceid
   GROUP BY
       invoice_day_category;
   ```
   **Goal and Real-World Application:**  
   This query compares the average sales amount for invoices created on weekdays versus weekends and the total number of invoices for each category.  
   **Application:** Businesses can adjust their marketing and sales strategies based on customer purchasing patterns, plan promotional activities during high-sales periods, and optimize staffing and resource allocation accordingly.

### Conclusion

This analysis provides insights into various aspects of the Chinook database, including album and artist details, genre popularity, customer spending habits, and sales trends. These queries demonstrate a range of SQL techniques and provide a foundation for further analysis and potential dashboard creation in tools like PowerBI.

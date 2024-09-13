Here are the SQL queries for your requests:

25. **Find the invoice with the highest value in 2006:**
   ```sql
   SELECT TOP 1 SOHD, TRIGIA
   FROM HOADON
   WHERE YEAR(NGHD) = 2006
   ORDER BY TRIGIA DESC;
   ```

26. **Find the name of the customer who made the highest value purchase in 2006:**
   ```sql
   SELECT HOTEN
   FROM KHACHHANG
   WHERE MAKH = (
       SELECT TOP 1 MAKH
       FROM HOADON
       WHERE YEAR(NGHD) = 2006
       ORDER BY TRIGIA DESC
   );
   ```

27. **List the top 3 customers (MAKH, HOTEN) sorted by descending sales:**
   ```sql
   SELECT TOP 3 MAKH, HOTEN
   FROM KHACHHANG
   ORDER BY DOANHSO DESC;
   ```

28. **List products (MASP, TENSP) with prices in the top 3 highest prices:**
   ```sql
   SELECT MASP, TENSP
   FROM SANPHAM
   WHERE GIA IN (
       SELECT TOP 3 GIA
       FROM SANPHAM
       ORDER BY GIA DESC
   );
   ```

29. **List products (MASP, TENSP) from "Thai Lan" with prices in the top 3 highest prices:**
   ```sql
   SELECT MASP, TENSP
   FROM SANPHAM
   WHERE NUOCSX = 'Thai Lan' AND GIA IN (
       SELECT TOP 3 GIA
       FROM SANPHAM
       ORDER BY GIA DESC
   );
   ```
Here are the SQL queries for your additional requests:

30. **List products (MASP, TENSP) from "Trung Quoc" with prices in the top 3 highest prices of products from "Trung Quoc":**
   ```sql
   SELECT MASP, TENSP
   FROM SANPHAM
   WHERE NUOCSX = 'Trung Quoc' AND GIA IN (
       SELECT TOP 3 GIA
       FROM SANPHAM
       WHERE NUOCSX = 'Trung Quoc'
       ORDER BY GIA DESC
   );
   ```

31. **List customers in the top 3 sales rankings:**
   ```sql
   SELECT TOP 3 MAKH, HOTEN
   FROM KHACHHANG
   ORDER BY DOANHSO DESC;
   ```

32. **Calculate the total number of products from "Trung Quoc":**
   ```sql
   SELECT SUM(SL) AS TotalProducts
   FROM CTHD
   WHERE MASP IN (
       SELECT MASP
       FROM SANPHAM
       WHERE NUOCSX = 'Trung Quoc'
   );
   ```

33. **Calculate the total number of products for each country of manufacture:**
   ```sql
   SELECT NUOCSX, SUM(SL) AS TotalProducts
   FROM CTHD
   JOIN SANPHAM ON CTHD.MASP = SANPHAM.MASP
   GROUP BY NUOCSX;
   ```

34. **For each country of manufacture, find the highest, lowest, and average prices of products:**
   ```sql
   SELECT NUOCSX, MAX(GIA) AS MaxPrice, MIN(GIA) AS MinPrice, AVG(GIA) AS AvgPrice
   FROM SANPHAM
   GROUP BY NUOCSX;
   ```

35. **Calculate daily sales revenue:**
   ```sql
   SELECT NGHD, SUM(TRIGIA) AS DailyRevenue
   FROM HOADON
   GROUP BY NGHD;
   ```

36. **Calculate the total quantity of each product sold in October 2006:**
   ```sql
   SELECT MASP, SUM(SL) AS TotalQuantity
   FROM CTHD
   JOIN HOADON ON CTHD.SOHD = HOADON.SOHD
   WHERE YEAR(NGHD) = 2006 AND MONTH(NGHD) = 10
   GROUP BY MASP;
   ```

37. **Calculate monthly sales revenue for each month in 2006:**
   ```sql
   SELECT MONTH(NGHD) AS Month, SUM(TRIGIA) AS MonthlyRevenue
   FROM HOADON
   WHERE YEAR(NGHD) = 2006
   GROUP BY MONTH(NGHD);
   ```

38. **Find invoices that include at least 4 different products:**
   ```sql
   SELECT SOHD
   FROM CTHD
   GROUP BY SOHD
   HAVING COUNT(DISTINCT MASP) >= 4;
   ```

39. **Find invoices with 3 products from "Viet Nam" (3 different products):**
   ```sql
   SELECT SOHD
   FROM CTHD
   JOIN SANPHAM ON CTHD.MASP = SANPHAM.MASP
   WHERE NUOCSX = 'Viet Nam'
   GROUP BY SOHD
   HAVING COUNT(DISTINCT MASP) >= 3;
   ```

40. **Find the customer (MAKH, HOTEN) with the most purchases:**
   ```sql
   SELECT TOP 1 MAKH, HOTEN, COUNT(SOHD) AS NumberOfPurchases
   FROM HOADON
   JOIN KHACHHANG ON HOADON.MAKH = KHACHHANG.MAKH
   GROUP BY MAKH, HOTEN
   ORDER BY NumberOfPurchases DESC;
   ```

Feel free to adjust these queries based on your actual database schema or any additional requirements you might have!

Here are the SQL queries for your additional requests:

41. **Which month in 2006 had the highest sales revenue?**
   ```sql
   SELECT TOP 1 MONTH(NGHD) AS Month, SUM(TRIGIA) AS TotalRevenue
   FROM HOADON
   WHERE YEAR(NGHD) = 2006
   GROUP BY MONTH(NGHD)
   ORDER BY TotalRevenue DESC;
   ```

42. **Find the product (MASP, TENSP) with the lowest total quantity sold in 2006:**
   ```sql
   SELECT MASP, TENSP
   FROM SANPHAM
   WHERE MASP = (
       SELECT TOP 1 MASP
       FROM CTHD
       JOIN SANPHAM ON CTHD.MASP = SANPHAM.MASP
       JOIN HOADON ON CTHD.SOHD = HOADON.SOHD
       WHERE YEAR(NGHD) = 2006
       GROUP BY MASP
       ORDER BY SUM(SL) ASC
   );
   ```

43. **For each country of manufacture, find the product (MASP, TENSP) with the highest price:**
   ```sql
   SELECT NUOCSX, MASP, TENSP
   FROM SANPHAM
   WHERE (NUOCSX, GIA) IN (
       SELECT NUOCSX, MAX(GIA)
       FROM SANPHAM
       GROUP BY NUOCSX
   );
   ```

44. **Find countries of manufacture that produce at least 3 products with different prices:**
   ```sql
   SELECT NUOCSX
   FROM SANPHAM
   GROUP BY NUOCSX
   HAVING COUNT(DISTINCT GIA) >= 3;
   ```

45. **Among the top 10 highest-grossing customers, find the one with the most purchases:**
   ```sql
   SELECT TOP 1 MAKH, HOTEN, COUNT(SOHD) AS NumberOfPurchases
   FROM HOADON
   JOIN KHACHHANG ON HOADON.MAKH = KHACHHANG.MAKH
   WHERE MAKH IN (
       SELECT TOP 10 MAKH
       FROM KHACHHANG
       ORDER BY DOANHSO DESC
   )
   GROUP BY MAKH, HOTEN
   ORDER BY NumberOfPurchases DESC;
   ```

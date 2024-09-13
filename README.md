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

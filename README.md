Dưới đây là các câu lệnh SQL cho từng yêu cầu của bạn, bao gồm số câu hỏi trong các chú thích và tiêu đề rõ ràng cho từng câu lệnh:

### 25. Tìm số hóa đơn có trị giá cao nhất trong năm 2006:
```sql
-- 25. [SAI] Tìm số hóa đơn có trị giá cao nhất trong năm 2006
SELECT TOP 1 SOHD, TRIGIA
FROM HOADON
WHERE YEAR(NGHD) = 2006
ORDER BY TRIGIA DESC;

-- 25. Tìm số hóa đơn có trị giá cao nhất trong năm 2006
SELECT SOHD, TRIGIA
FROM HOADON
WHERE YEAR(NGHD) = 2006
  AND TRIGIA = (
      SELECT MAX(TRIGIA)
      FROM HOADON
      WHERE YEAR(NGHD) = 2006
  );


```

### 26. Tìm họ tên khách hàng đã mua hóa đơn có trị giá cao nhất trong năm 2006:
```sql
-- 26. [SAI] Tìm họ tên khách hàng đã mua hóa đơn có trị giá cao nhất trong năm 2006
SELECT HOTEN
FROM KHACHHANG
WHERE MAKH = (
    SELECT TOP 1 MAKH
    FROM HOADON
    WHERE YEAR(NGHD) = 2006
    ORDER BY TRIGIA DESC
);

-- 26. Tìm họ tên khách hàng đã mua hóa đơn có trị giá cao nhất trong năm 2006
SELECT HOTEN
FROM KHACHHANG
WHERE MAKH IN (
    SELECT MAKH
    FROM HOADON
    WHERE YEAR(NGHD) = 2006
      AND TRIGIA IN (
          SELECT TOP 1 TRIGIA
          FROM HOADON
          WHERE YEAR(NGHD) = 2006
          ORDER BY TRIGIA DESC
      )
);

Cách nhanh nhất
         SELECT TOP 1 WITH TIES *
          FROM HOADON JOIN KHACHHANG On HOADON.MAKH = KHACHHANG.MAKH
          WHERE YEAR(NGHD) = 2006 
          ORDER BY TRIGIA DESC

```

### 27. In ra danh sách 3 khách hàng đầu tiên (MAKH, HOTEN) sắp xếp theo doanh số giảm dần:
```sql
-- 27. In ra danh sách 3 khách hàng đầu tiên (MAKH, HOTEN) sắp xếp theo doanh số giảm dần
SELECT TOP 3 MAKH, HOTEN
FROM KHACHHANG
ORDER BY DOANHSO DESC;
```

### 28. In ra danh sách các sản phẩm (MASP, TENSP) có giá bán bằng 1 trong 3 mức giá cao nhất:
```sql
-- 28 [SAI]. In ra danh sách các sản phẩm (MASP, TENSP) có giá bán bằng 1 trong 3 mức giá cao nhất
SELECT MASP, TENSP
FROM SANPHAM
WHERE GIA IN (
    SELECT TOP 3 GIA
    FROM SANPHAM
    ORDER BY GIA DESC
);

-- 28. In ra danh sách các sản phẩm (MASP, TENSP) có giá bán bằng 1 trong 3 mức giá cao nhất (khác nhau)
SELECT MASP, TENSP
FROM SANPHAM
WHERE GIA IN (
    SELECT DISTINCT TOP 3 GIA
    FROM SANPHAM
    ORDER BY GIA DESC
);

```

### 29. In ra danh sách các sản phẩm (MASP, TENSP) do “Thai Lan” sản xuất có giá bằng 1 trong 3 mức giá cao nhất:
```sql
-- 29.[SAI] In ra danh sách các sản phẩm (MASP, TENSP) do “Thai Lan” sản xuất có giá bằng 1 trong 3 mức giá cao nhất
SELECT MASP, TENSP
FROM SANPHAM
WHERE NUOCSX = 'Thai Lan' AND GIA IN (
    SELECT DISTINCT TOP 3 GIA
    FROM SANPHAM
    WHERE NUOCSX = 'Thai Lan'
    ORDER BY GIA DESC
);

-- 29. In ra danh sách các sản phẩm (MASP, TENSP) do “Thai Lan” sản xuất có giá bằng 1 trong 3 mức giá cao nhất
SELECT MASP, TENSP
FROM SANPHAM
WHERE NUOCSX = 'Thai Lan' AND GIA IN (
    SELECT DISTINCT TOP 3 GIA
    FROM SANPHAM
    ORDER BY GIA DESC
);
```

### 30. In ra danh sách các sản phẩm (MASP, TENSP) do “Trung Quoc” sản xuất có giá bằng 1 trong 3 mức giá cao nhất (của sản phẩm do “Trung Quoc” sản xuất):
```sql
-- 30. In ra danh sách các sản phẩm (MASP, TENSP) do “Trung Quoc” sản xuất có giá bằng 1 trong 3 mức giá cao nhất (của sản phẩm do “Trung Quoc” sản xuất)
SELECT MASP, TENSP
FROM SANPHAM
WHERE NUOCSX = 'Trung Quoc' AND GIA IN (
    SELECT DISTINCT TOP 3 GIA
    FROM SANPHAM
    WHERE NUOCSX = 'Trung Quoc'
    ORDER BY GIA DESC
);
```

### 31. Tìm khách hàng nằm trong 3 hạng cao nhất (xếp hạng theo doanh số):
```sql
-- 31. [SAI] Tìm khách hàng nằm trong 3 hạng cao nhất (xếp hạng theo doanh số)
SELECT TOP 3 MAKH, HOTEN
FROM KHACHHANG
ORDER BY DOANHSO DESC;

-- 31. Tìm khách hàng nằm trong 3 hạng cao nhất (xếp hạng theo doanh số)
SELECT MAKH, HOTEN
FROM KHACHHANG
WHERE DOANHSO IN (
    SELECT DISTINCT TOP 3 DOANHSO
    FROM KHACHHANG
    ORDER BY DOANHSO DESC
);

```

### 32. Tính tổng số sản phẩm do “Trung Quoc” sản xuất:
```sql
-- 32 [OK]. Tính tổng số sản phẩm do “Trung Quoc” sản xuất
SELECT SUM(SL) AS ketqua
FROM CTHD
WHERE MASP IN (
    SELECT MASP
    FROM SANPHAM
    WHERE NUOCSX = 'Trung Quoc'
);

-- 32 [CÁCH KHÁC]. Tính tổng số sản phẩm do “Trung Quoc” sản xuất
SELECT SUM(SL) AS ketqua
FROM CTHD, SANPHAM WHERE CTHD.MASP = SANPHAM.MASP AND nuocsx = 'Trung Quoc'

-- 32 [CÁCH TỐT NHẤT]. Tính tổng số sản phẩm do “Trung Quốc” sản xuất
SELECT SUM(CTHD.SL) AS ketqua
FROM CTHD
JOIN SANPHAM ON CTHD.MASP = SANPHAM.MASP
WHERE SANPHAM.NUOCSX = 'Trung Quoc';

```

### 33. Tính tổng số sản phẩm của từng nước sản xuất:
```sql
-- 33. Tính tổng số sản phẩm của từng nước sản xuất
SELECT NUOCSX, SUM(SL) AS TotalProducts
FROM CTHD
JOIN SANPHAM ON CTHD.MASP = SANPHAM.MASP
GROUP BY NUOCSX;
```

### 34. Với từng nước sản xuất, tìm giá bán cao nhất, thấp nhất, trung bình của các sản phẩm:
```sql
-- 34. Với từng nước sản xuất, tìm giá bán cao nhất, thấp nhất, trung bình của các sản phẩm
SELECT NUOCSX, MAX(GIA) AS MaxPrice, MIN(GIA) AS MinPrice, AVG(GIA) AS AvgPrice
FROM SANPHAM
GROUP BY NUOCSX;
```

### 35. Tính doanh thu bán hàng mỗi ngày:
```sql
-- 35. Tính doanh thu bán hàng mỗi ngày
SELECT NGHD, SUM(TRIGIA) AS DailyRevenue
FROM HOADON
GROUP BY NGHD;
```

### 36. Tính tổng số lượng của từng sản phẩm bán ra trong tháng 10/2006:
```sql
-- 36. Tính tổng số lượng của từng sản phẩm bán ra trong tháng 10/2006
SELECT MASP, SUM(SL) AS TotalQuantity
FROM CTHD
JOIN HOADON ON CTHD.SOHD = HOADON.SOHD
WHERE YEAR(NGHD) = 2006 AND MONTH(NGHD) = 10
GROUP BY MASP;
```

### 37. Tính doanh thu bán hàng của từng tháng trong năm 2006:
```sql
-- 37. Tính doanh thu bán hàng của từng tháng trong năm 2006
SELECT MONTH(NGHD) AS Month, SUM(TRIGIA) AS MonthlyRevenue
FROM HOADON
WHERE YEAR(NGHD) = 2006
GROUP BY MONTH(NGHD);
```

### 38. Tìm hóa đơn có mua ít nhất 4 sản phẩm khác nhau:
```sql
-- 38. Tìm hóa đơn có mua ít nhất 4 sản phẩm khác nhau
SELECT SOHD
FROM CTHD
GROUP BY SOHD
HAVING COUNT(DISTINCT MASP) >= 4;

-- 38[CÁCH NÊN LÀM]. Tìm hóa đơn có mua ít nhất 4 sản phẩm khác nhau
SELECT SOHD, COUNT(*) AS SOSP
FROM CTHD
GROUP BY SOHD
HAVING COUNT(DISTINCT MASP) >= 4;
```

### 39. Tìm hóa đơn có mua 3 sản phẩm do “Việt Nam” sản xuất (3 sản phẩm khác nhau):
```sql
-- 39. Tìm hóa đơn có mua 3 sản phẩm do “Việt Nam” sản xuất (3 sản phẩm khác nhau)
SELECT SOHD
FROM CTHD
JOIN SANPHAM ON CTHD.MASP = SANPHAM.MASP
WHERE NUOCSX = 'Viet Nam'
GROUP BY SOHD
HAVING COUNT(DISTINCT CTHD.MASP) = 3;
```

### 40. Tìm khách hàng (MAKH, HOTEN) có số lần mua hàng nhiều nhất:
```sql
-- 40. [ĐÚNG NHƯNG CHƯA ĐỦ] Tìm khách hàng (MAKH, HOTEN) có số lần mua hàng nhiều nhất
SELECT MAKH, HOTEN
FROM KHACHHANG
WHERE MAKH IN (
    SELECT TOP 1 MAKH
    FROM HOADON
    GROUP BY MAKH
    ORDER BY COUNT(SOHD) DESC
);

SELECT MAKH, HOTEN FROM KHACHHANG 
WHERE MAKH IN 
(
   SELECT TOP 1 with ties MAKH  
   FROM HOADON
   GROUP BY MAKH order by COUNT(SOHD) DESC 
)
```

### 41. Tháng mấy trong năm 2006, doanh số bán hàng cao nhất?
```sql
-- 41 [ĐÚNG NHƯNG CHƯA ĐỦ]. Tháng mấy trong năm 2006, doanh số bán hàng cao nhất?
SELECT TOP 1 MONTH(NGHD) AS Month, SUM(TRIGIA) AS TotalRevenue
FROM HOADON
WHERE YEAR(NGHD) = 2006
GROUP BY MONTH(NGHD)
ORDER BY TotalRevenue DESC

-- 41. Tháng nào trong năm 2006 có doanh số bán hàng cao nhất? (Bao gồm các tháng có cùng doanh số cao nhất)
SELECT MONTH(NGHD) AS Month, 
       SUM(TRIGIA) AS TotalRevenue
FROM HOADON
WHERE YEAR(NGHD) = 2006
GROUP BY MONTH(NGHD)
HAVING SUM(TRIGIA) = (
    SELECT MAX(MonthlyTotal)
    FROM (
        SELECT SUM(TRIGIA) AS MonthlyTotal
        FROM HOADON
        WHERE YEAR(NGHD) = 2006
        GROUP BY MONTH(NGHD)
    ) AS MonthlySales
);

-- 41 [NHANH]. Tháng nào trong năm 2006 có doanh số bán hàng cao nhất? (Bao gồm các tháng có cùng doanh số cao nhất)
SELECT TOP 1 WITH TIES
    MONTH(NGHD) AS Thang,
    SUM(TRIGIA) AS TongDoanhSo
FROM HOADON
WHERE YEAR(NGHD) = 2006
GROUP BY MONTH(NGHD)
ORDER BY SUM(TRIGIA) DESC;
```

### 42. Tìm sản phẩm (MASP, TENSP) có tổng số lượng bán ra thấp nhất trong năm 2006:
```sql
-- 42 [ĐÚNG NHƯNG CHƯA ĐỦ]. Tìm sản phẩm (MASP, TENSP) có tổng số lượng bán ra thấp nhất trong năm 2006
SELECT TOP 1 SANPHAM.MASP, TENSP
FROM SANPHAM
JOIN CTHD ON SANPHAM.MASP = CTHD.MASP
JOIN HOADON ON CTHD.SOHD = HOADON.SOHD
WHERE YEAR(NGHD) = 2006
GROUP BY SANPHAM.MASP, TENSP
ORDER BY SUM(SL) ASC;

-- 42. Tìm sản phẩm (MASP, TENSP) có tổng số lượng bán ra thấp nhất trong năm 2006
SELECT MASP, TENSP
FROM SANPHAM
WHERE MASP IN (
    SELECT MASP
    FROM CTHD
    JOIN HOADON ON CTHD.SOHD = HOADON.SOHD
    WHERE YEAR(NGHD) = 2006
    GROUP BY MASP
    HAVING SUM(SL) = (
        SELECT MIN(TongSL)
        FROM (
            SELECT MASP, SUM(SL) AS TongSL
            FROM CTHD
            JOIN HOADON ON CTHD.SOHD = HOADON.SOHD
            WHERE YEAR(NGHD) = 2006
            GROUP BY MASP
        ) AS ProductSales
    )
);

-- 42 [CÁCH KHÁC]. Tìm sản phẩm (MASP, TENSP) có tổng số lượng bán ra thấp nhất trong năm 2006
SELECT TOP 1 WITH TIES
    SANPHAM.MASP,
    SANPHAM.TENSP,
    SUM(CTHD.SL) AS TongSoLuong
FROM CTHD
JOIN HOADON ON CTHD.SOHD = HOADON.SOHD
JOIN SANPHAM ON CTHD.MASP = SANPHAM.MASP
WHERE YEAR(HOADON.NGHD) = 2006
GROUP BY SANPHAM.MASP, SANPHAM.TENSP
ORDER BY TongSoLuong;


```

### 43. Mỗi nước sản xuất, tìm sản phẩm (MASP, TENSP) có giá bán cao nhất:
```sql
-- 43. Mỗi nước sản xuất, tìm sản phẩm (MASP, TENSP) có giá bán cao nhất
SELECT MASP, TENSP, NUOCSX, GIA
FROM SANPHAM AS sp
WHERE GIA = (
    SELECT MAX(GIA)
    FROM SANPHAM
    WHERE NUOCSX = sp.NUOCSX
);

```

### 44. Tìm nước sản xuất sản xuất ít nhất 3 sản phẩm có giá bán khác nhau:
```sql
-- 44. Tìm nước sản xuất sản xuất ít nhất 3 sản phẩm có giá bán khác nhau
SELECT NUOCSX
   FROM SANPHAM
   GROUP BY NUOCSX
   HAVING COUNT(DISTINCT GIA) >= 3;
   ```


45. **Trong 10 khách hàng có doanh số cao nhất, tìm khách hàng có số lần mua hàng nhiều nhất:**
   ```sql
-- 45. Trong 10 khách hàng có doanh số cao nhất, tìm khách hàng có số lần mua hàng nhiều nhất:
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

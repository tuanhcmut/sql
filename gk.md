### 2c
```sql
--- Bước 1: Bảng chính là InvoiceDetail AS A, đem join với Beverage AS B để lấy TypeId,
--- đem join với BeverageDetail AS BT để lấy TypeName dựa trên TypeId,
---- sắp xếp giảm dần (cao xuống thấp) theo cột quantity (nhớ DESC)

SELECT A.quantity, BT.typename FROM InvoiceDetail AS A 
JOIN Beverage AS B ON B.BevID = A.BevID
JOIN BeverageType ÁS BT ON B.TypeID = BT.TypeID
ORDER BY A.quantity DESC
-- Bước 2: Lúc này thu được hai cột (Typename và Quantity). Giờ nhóm lại (group by) theo TypeName.
-- Cột Typename được phép select, còn Quantity do không group by nên chỉ được dùng SUM/MIN/MAX/COUNT mà thôi
-- Ta dùng SUM(QUANTITY) để xuất ra Tổng số sp bán ra theo từng loại sp. ORDER BY cũng theo 
SELECT SUM(A.quantity), BT.typename FROM InvoiceDetail AS A 
JOIN Beverage AS B ON B.BevID = A.BevID
JOIN BeverageType AS BT ON B.TypeID = BT.TypeID
GROUP BY BT.TypeName
ORDER BY A.quantity DESC
-- Lúc này sẽ thấy chạy do ORDER BY với còn các cột còn lại thì phải theo SUM/MIN/MAX/COUNT...
SELECT SUM(A.quantity), BT.typename FROM InvoiceDetail AS A 
JOIN Beverage AS B ON B.BevID = A.BevID
JOIN BeverageType AS BT ON B.TypeID = BT.TypeID
GROUP BY BT.TypeName
ORDER BY SUM(A.quantity) DESC
-- Ta thu được một bảng sắp xếp từ cao xuống thấp, giờ chọn ra top 1 cao nhất, (có thể đồng hạng nhất) ,
-- thêm chữ "top 1 with ties" kế bên chữ select là xong
SELECT TOP 1 WITH TIES SUM(A.quantity) AS SOLUONG, BT.typename FROM InvoiceDetail AS A 
JOIN Beverage AS B ON B.BevID = A.BevID
JOIN BeverageType AS BT ON B.TypeID = BT.TypeID
GROUP BY BT.TypeName
ORDER BY SUM(A.quantity) DESC

```

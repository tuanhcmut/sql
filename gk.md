### 2a
```sql
-- Doanh số từng sản phẩm
-- Cách 1: dùng 1 bảng duy nhất, nhóm dựa trên BevId do đề không yêu cầu xuất ra tên sp mà chỉ cần theo sp là đc
SELECT BevID, SUM(quantity*price) AS DOANHSO
FROM InvoiceDetail
GROUP BY BevId

-- Cách 2: dùng bảng Beverage làm bảng chính để LEFT join với InvoiceDetail, cái này sẽ đầy đủ sp (trong trường hợp sp nào k bán được thì doanhso = null)
SELECT B.BevId, SUM(ID.Quantity * ID.Price) AS DOANHSO
FROM Beverage AS B
LEFT JOIN InvoiceDetail AS ID ON B.BevID = ID.BevID
GROUP BY B.BevID;

```
 

### 2b
```sql
-- Mã hóa đơn mua tất cả sản phẩm thuộc loại nước khoáng/nước suối
-- B1: Xác định hai bảng cần xét (R và S), nó phải có chung cột nào đó (vd : BevID)
-- B2: Xác định cột cần xuất/select (A), cột chung giữa hai bảng là gì (B)
-- B3: Xem có điều kiện gì nữa không, nếu có thì dùng where trong công thức (điều kiện thường cũng đơn giản)
-- B4: áp dụng công thức nhữ đã hướng dẫn (thế R, S , A, B, điều kiện where nếu có)

SELECT InvoiceDetail.InvCode
FROM InvoiceDetail
WHERE InvoiceDetail.BevID IN (
    SELECT Beverage.BevID
    FROM Beverage
    WHERE TypeID = '0002' -- Loại nước khoáng/nước suối
)
GROUP BY InvoiceDetail.InvCode
HAVING COUNT(DISTINCT InvoiceDetail.BevID) = (
    SELECT COUNT(Beverage.BevID)
    FROM Beverage
    WHERE TypeID = '0002'
);

```


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

### 1a
```sql
-- Xóa sp không bán được , đọc file topic 6 , phần Phép trừ vd2, sử dụng NOT IN (không cần đọc mấy cái khác)
-- Nhận xét: SP không bán được  = tất cả SP (có trong bảng Beverage) - SP bán được (có trong bảng InvoiceDetail)
-- với A là bảng chứa tất cả các sp, còn B là bảng chứa các SP bán được, C là cột chung giữa hai bảng này
-- Suy ra A là bảng Beverage, B là bảng InvoiceDetail, C là cột BevId

SELECT * FROM A
WHERE C NOT IN (
    SELECT C
    FROM B
);

--- B1. Select trước để thử xem:

SELECT * FROM Beverage
WHERE BevID NOT IN (
    SELECT BevID
    FROM InvoiceDetail
);

--- B2. Thấy đúng rồi (các sp xuất ra sau khi chạy đều k nằm trong bảng B) thì chuyển hóa SELECT *  thành DELETE

DELETE FROM Beverage
WHERE BevID NOT IN (
    SELECT DISTINCT BevID
    FROM InvoiceDetail
);

```


### 1b
```sql
--Giảm giá 2000 nếu giá sau giảm không thấp hơn 6000, ngược lại giá mới là 6000
-- suy ra 2 trường hợp: nếu price - 2000 < 6000 thì price = 6000 ngược lại price = price - 2000
-- Hoặc suy như vậy: nếu price - 2000 >= 6000 thì price = price - 6000 ngược lại price = 6000
-- b1: select trước để xem câu lệnh (case ... end) có đúng không, nhớ điều kiện where là nước có ga

SELECT *, CASE
 WHEN PRICE - 2000 >= 6000 THEN PRICE - 2000
 ELSE 6000
END  
FROM Beverage
WHERE typeid=0001

-- b2: Sau khi chạy xong, thấy giá mới đều từ 6000 trở lên rồi mới chuyển hóa sang lệnh UPDATE [tên bảng] SET PRICE = ...

UPDATE Beverage
SET Beverage.PRICE = CASE
	WHEN PRICE - 2000 < 6000 THEN 6000
    ELSE PRICE - 2000
END 
WHERE typeid=0001

```


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
-- Ta dùng SUM(QUANTITY) để xuất ra Tổng số sp bán ra theo từng loại sp. ORDER BY mà giữ nguyên như vậy là sai
SELECT SUM(A.quantity), BT.typename FROM InvoiceDetail AS A 
JOIN Beverage AS B ON B.BevID = A.BevID
JOIN BeverageType AS BT ON B.TypeID = BT.TypeID
GROUP BY BT.TypeName
ORDER BY A.quantity DESC
-- Lúc này sẽ thấy chạy lỗi do ORDER BY với còn các cột còn lại thì phải theo SUM/MIN/MAX/COUNT...
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

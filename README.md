CREATE DATABASE Perpustakaan;

USE Perpustakaan;

CREATE TABLE genres (
  id char(5) PRIMARY KEY,
  genre_name varchar(50) NOT NULL,
  description varchar(255) NOT NULL
);

CREATE TABLE positions (
  id char(5) PRIMARY KEY,
  position_name varchar(50) NOT NULL,
  admin_access boolean NOT NULL
);

CREATE TABLE members (
  id char(5) NOT NULL,
  name varchar(50) NOT NULL,
  email varchar(50) NOT NULL,
  gender char(1) NOT NULL,
  address varchar(100) NOT NULL,
  CONSTRAINT PK_members PRIMARY KEY (id)
);

CREATE TABLE books (
  id char(5) PRIMARY KEY,
  isbn char(13) NOT NULL,
  title varchar(100)NOT NULL,
  author_id varchar(50) NOT NULL,
  year_published YEAR NOT NULL,
  synopsis varchar(255) NOT NULL,
  stock int NOT NULL,
  genre_id char(5) NOT NULL,
  CONSTRAINT FK_books_genre_id FOREIGN KEY (genre_id) REFERENCES genres(id)
    ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE employees (
  id char(5) NOT NULL,
  name varchar(50) NOT NULL,
  email varchar(50) NOT NULL,
  gender char(1) NOT NULL,
  address varchar(100) NOT NULL,
  position_id char(5) NOT NULL,
  CONSTRAINT PK_employees PRIMARY KEY (id),
  CONSTRAINT FK_employees_position_id FOREIGN KEY (position_id) REFERENCES positions(id)
    ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE borrows (
  id char(5) PRIMARY KEY,
  borrow_date DATE NOT NULL,
  return_date DATE,
  due_date DATE NOT NULL,
  fine DECIMAL(10,2),
  member_id char(5) NOT NULL,
  employee_id char(5) NOT NULL,
  CONSTRAINT FK_borrows_member_id FOREIGN KEY (member_id) REFERENCES members(id)
    ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT FK_borrows_employee_id FOREIGN KEY (employee_id) REFERENCES employees(id)
    ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE books_borrows (
  book_id char(5) NOT NULL,
  borrow_id char(5) NOT NULL,
  CONSTRAINT PK_books_borrows PRIMARY KEY (book_id, borrow_id),
  CONSTRAINT FK_books_borrows_book_id FOREIGN KEY (book_id) REFERENCES books(id)
    ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT FK_books_borrows_borrows_id FOREIGN KEY (borrow_id) REFERENCES borrows(id)
    ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE phone_numbers (
  phone_number varchar(13) PRIMARY KEY,
  member_id char(5) NOT NULL,
  employee_id char(5) NOT NULL,
  CONSTRAINT FK_phone_numbers_member_id FOREIGN KEY (member_id) REFERENCES members(id)
    ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT FK_phone_numbers_employee_id FOREIGN KEY (employee_id) REFERENCES employees(id)
    ON DELETE CASCADE ON UPDATE CASCADE
);

-- 2.
CREATE TABLE authors (
  id char(5) PRIMARY KEY,
  author_name varchar(50) NOT NULL,
  nationality varchar(30) NOT NULL,
  birthdate date NOT NULL
);

CREATE TABLE publishers (
  id char(5) PRIMARY KEY,
  name varchar(50) NOT NULL,
  address varchar(100) NOT NULL,
  country varchar(100) NOT NULL,
  email varchar(50) NOT NULL
);

ALTER TABLE books
  ADD publisher_id char(5) NOT NULL;

ALTER TABLE books
  ADD CONSTRAINT FK_books_author_id FOREIGN KEY (author_id) REFERENCES authors(id)
  ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE books
  ADD CONSTRAINT FK_books_publisher_id FOREIGN KEY (publisher_id) REFERENCES publishers(id)
  ON DELETE CASCADE ON UPDATE CASCADE;

-- 3.
ALTER TABLE phone_numbers 
  DROP CONSTRAINT FK_phone_numbers_member_id;

ALTER TABLE borrows 
  DROP CONSTRAINT FK_borrows_member_id;

ALTER TABLE members 
  DROP PRIMARY KEY;

ALTER TABLE members 
  CHANGE id NIK char(16) NOT NULL;

ALTER TABLE members 
  ADD CONSTRAINT PK_members PRIMARY KEY (NIK);

ALTER TABLE borrows 
  MODIFY member_id char(16) NOT NULL;

ALTER TABLE phone_numbers 
  MODIFY member_id char(16) NOT NULL;

ALTER TABLE phone_numbers 
  ADD CONSTRAINT FK_phone_numbers_member_id FOREIGN KEY (member_id) REFERENCES members(NIK)
  ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE borrows 
  ADD CONSTRAINT FK_borrows_member_id FOREIGN KEY (member_id) REFERENCES members(NIK)
  ON DELETE CASCADE ON UPDATE CASCADE;

-- 4.
DROP TABLE phone_numbers;

ALTER TABLE members
  ADD phone_numbers varchar(13) NOT NULL;

ALTER TABLE employees
  ADD phone_numbers varchar(13) NOT NULL;

-- 5.
INSERT INTO genres (ID, genre_name, description)
  VALUES
  ('GR001','Sejarah','Buku yang mengulas peristiwa atau era tertentu, memberikan wawasan tentang masa lalu dan dampaknya pada masa kini. Biasanya disertai data, analisis, dan narasi historis yang mendalam.'),
  ('GR002','Fiksi','Cerita imajinatif yang dibuat oleh penulis, tidak didasarkan pada fakta nyata. Genre ini mencakup subgenre seperti roman, fantasi, dan petualangan, dengan fokus pada karakter dan alur cerita.'),
  ('GR003','Pengembangan Diri','Buku yang memberikan panduan untuk meningkatkan kualitas hidup atau keterampilan pembacanya. Menyajikan saran praktis tentang topik seperti motivasi, manajemen waktu, dan pemecahan masalah pribadi.'),
  ('GR004','Biografi','Menceritakan kehidupan seseorang dengan detail dari lahir hingga akhir hidupnya atau saat ini. Disusun berdasarkan fakta, kisah ini menggambarkan pengalaman, perjuangan, dan prestasi tokoh tersebut.'),
  ('GR005','Referensi','Buku yang berfungsi sebagai sumber rujukan lengkap, seperti kamus, ensiklopedia, atau handbook. Memuat data, definisi, atau konsep yang dapat diakses sesuai kebutuhan, tanpa harus dibaca secara berurutan.');

INSERT INTO authors (ID, author_name, nationality, birthdate)
  VALUES
  ('AU001','Michael Brown','Amerika','1990-03-30'),
  ('AU002','Andi Saputra','Indonesia','1988-10-02'),
  ('AU003','Yuki Nakamura','Jepang','1973-01-22'),
  ('AU004','Kim Min Joon','Korea Selatan','1982-04-18'),
  ('AU005','James Williams','Inggris','1962-12-04');

INSERT INTO publishers (ID, name, address, country, email)
  VALUES
  ('PB001','Silver Oak Publishing','1234 Elm Street','Amerika Serikat','silveroak1234@gmail.com'),
  ('PB002','Blue Sky Books','9012 Pine Road','Jepang','bluesky9012@gmail.com'),
  ('PB003','Green Leaf Press','5678 Maple Avenue','Indonesia','greenleaf5678@gmail.com'),
  ('PB004','Golden Dragon Publishing','3456 Bamboo Lane','China','goldendragon3456@gmail.com'),
  ('PB005','Red River Press','7890 Cherry Blossom Drive','Perancis','redriver7890@gmail.com');

INSERT INTO books (id, isbn, title, year_published, synopsis, stock, genre_id, author_id, publisher_id)
  VALUES
  ('BK001','9780123456789','Jejak Peradaban: Kisah Perjalanan Sejarah Indonesia','2010','Menggali sejarah Indonesia, buku ini menyajikan peristiwa penting dan tokoh berpengaruh yang membentuk peradaban bangsa, membawa pembaca memahami akar budaya dan identitas Indonesia.','5','GR001','AU002','PB003'),
  ('BK002','9781234567890','Small Steps, Big Changes: Building Positive Habits','2005','Temukan kekuatan dari kebiasaan kecil yang berdampak besar. Buku ini memberikan strategi praktis untuk membangun kebiasaan positif yang dapat mengubah hidup dan meningkatkan kesejahteraan secara keseluruhan.','3','GR003','AU003','PB002'),
  ('BK003','9782345678901','Complete Dictionary of Information Technology Terms','2020','Panduan lengkap untuk istilah-istilah teknologi informasi, buku ini menjelaskan konsep-konsep dasar hingga istilah teknis yang kompleks, menjadikannya sumber referensi yang berguna bagi pelajar dan profesional.','7','GR005','AU001','PB001'),
  ('BK004','9783456789012','Breaking Barriers: The Inspiring Story of Nelson Mandela','2018','Kisah luar biasa Nelson Mandela, seorang pejuang kebebasan yang mengatasi rintangan untuk memperjuangkan kesetaraan dan keadilan. Sebuah perjalanan inspiratif tentang keberanian, pengorbanan, dan harapan.','2','GR004','AU005','PB001'),
  ('BK005','9784567890123','Light at the End of the Road: A Story of Hope','2024','Dalam perjalanan penuh tantangan, seorang tokoh menemukan kekuatan dari harapan dan persahabatan. Novel ini mengajak pembaca merenungkan arti kehidupan dan pentingnya percaya pada masa depan yang lebih baik.','4','GR002','AU004','PB004');

INSERT INTO positions (id, position_name, admin_access)
  VALUES
('PS001' ,'Pustakawan' ,True),
  ('PS002' ,'Kebersihan' ,False),
  ('PS003' ,'Keamanan' ,False);

INSERT INTO employees (id, name, email, gender, phone_numbers, address, position_id)
  VALUES
  ('EM001','Andi Gading','andi@gmail.com', 'P', '081628492610','Jl. Merdeka No.10','PS001'),
  ('EM002', 'Budi Sitombing','budi@gmail.com', 'L', '085864927581','Jl. Sukun Raya No.25','PS003'),
  ('EM003', 'Jennifer Tina','tina@gmail.com', 'P', '089603471812','Jl. Pahlawan No.5','PS001'),
  ('EM004', 'Lili Sari','lili@gmail.com', 'P', '088385793136','Jl. Cendana No.8','PS002'),
  ('EM005' ,'Alexander Agus','agus@gmail.com', 'L', '088273659814','Jl. Bunga No.12','PS001');

INSERT INTO members (NIK, name, email, gender, address, phone_numbers)
  VALUES
  ('3326162409040002', 'Citra Kirana', 'citra@gmail.com','P','Jl. Cinta No.45','088374628921'),
  ('3525017006950028','Jasmine Neroli','jasmine@gmail.com','P','Jl. Melati No.9','083285716164'),
  ('3525017006520020','Nico Parto','nico@gmail.com','L','Jl. Kenanga No.33','089675329117'),
  ('3305040901072053','Teddy Widodo','teddy@gmail.com','L','Jl. Anggrek No.17','085782306818'),
  ('3326161509700004','Beni Soeharti','beni@gmail.com','L','Jl. Raya No.56','0812345678919');


INSERT INTO borrows (id, borrow_date, return_date, due_date, fine, member_id, employee_id)
  VALUES
  ('BR001','2024-05-06','2024-05-20','2024-05-20',NULL,'3326162409040002','EM004'),
  ('BR002','2024-07-14','2024-07-29','2024-07-28','5000.00','3525017006950028','EM001'),
  ('BR003','2024-09-22','2024-10-08','2024-10-06','10000.00','3525017006520020','EM003'),
  ('BR004','2024-10-03','2024-10-20','2024-10-17','15000.00','3305040901072053','EM002'),
  ('BR005','2024-11-02',NULL,'2024-11-16',NULL,'3326161509700004','EM005');

INSERT INTO books_borrows (book_id, borrow_id)
  VALUES
  ('BK001','BR001'),
  ('BK003','BR002'),
  ('BK001','BR002'),
  ('BK004','BR003'),
  ('BK005','BR004'),
  ('BK002','BR005');

-- 6.
ALTER TABLE books_borrows 
  DROP FOREIGN KEY FK_books_borrows_borrows_id;

SET SQL_SAFE_UPDATES = 0;

DELETE FROM borrows WHERE ID LIKE 'BR%';
DELETE FROM books_borrows WHERE borrow_id LIKE 'BR%';

SET SQL_SAFE_UPDATES = 1;

ALTER TABLE borrows 
  DROP PRIMARY KEY;

ALTER TABLE borrows 
  MODIFY COLUMN ID INT NOT NULL AUTO_INCREMENT,
  ADD PRIMARY KEY (ID);

ALTER TABLE books_borrows
  MODIFY COLUMN borrow_id INT AUTO_INCREMENT NOT NULL;

ALTER TABLE books_borrows 
  ADD CONSTRAINT FK_books_borrows_borrows_id FOREIGN KEY (borrow_id) REFERENCES borrows(id)
  ON DELETE CASCADE ON UPDATE CASCADE;

INSERT INTO borrows (borrow_date, return_date, due_date, fine, member_id, employee_id) VALUES
('2024-05-06', '2024-05-20', '2024-05-20', NULL, '3326162409040002', 'EM004'),
('2024-07-14', '2024-07-29', '2024-07-28', 5000.00, '3525017006950028', 'EM001'),
('2024-09-22', '2024-10-08', '2024-10-06', 10000.00, '3525017006520020', 'EM003'),
('2024-10-03', '2024-10-20', '2024-10-17', 15000.00, '3305040901072053', 'EM002'),
('2024-11-02', NULL, '2024-11-16', NULL, '3326161509700004', 'EM005');

INSERT INTO books_borrows (book_id, borrow_id)
  VALUES
  ('BK001','1'),
  ('BK003','2'),
  ('BK001','2'),
  ('BK004','3'),
  ('BK005','4'),
  ('BK002','5');

-- 7.

UPDATE borrows SET
  return_date = NOW()
  WHERE member_id = '3326161509700004';

-- 8.

UPDATE borrows
  SET fine = NULL
  WHERE member_id = '3525017006520020' OR member_id = '3305040901072053';

-- 9.

INSERT INTO borrows (borrow_date, return_date, due_date, fine, member_id, employee_id)
  VALUES
  ('2024-11-03', NULL, '2024-11-17', NULL, '3326162409040002', 'EM005');

INSERT INTO books_borrows (book_id, borrow_id)
  VALUES 
  ('BK003', 6);

UPDATE books SET
  stock = (stock - 1)
  WHERE id = 'BK003';

-- 10.

INSERT INTO employees (id, name, email, gender, phone_numbers, address, position_id)
  VALUES
  ('EM006', 'Aspas Gata', 'aspasgata@gmail.com', 'L', '0895323390308', 'Jl. Badut No.62', 'PS001');

-- 11.

UPDATE employees SET
  name = 'Andi Haki',
  gender = 'L',
  phone_numbers = '0816348492611'
  WHERE id = 'EM001';

-- 12.

DELETE FROM members WHERE NIK = '3525017006950028';

DELETE FROM employees WHERE id = 'EM006';

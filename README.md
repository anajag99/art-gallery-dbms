# Art Gallery Database Management System (DBMS)

## Overview
The **Art Gallery DBMS** is a comprehensive database solution aimed at automating and optimizing the management of art galleries. It includes features for managing artwork inventory, sales tracking, artist profiles, employee data, and customer interactions. This project focuses on technical implementation using SQL for database design, data integrity, querying, and automation.

## Features
1. **Artwork Inventory Management**: 
   - Tracks artwork details such as title, artist, medium, dimensions, and location.
   - Manages artwork status (added, sold, moved).

2. **Sales and Revenue Monitoring**:
   - Tracks sales transactions and revenue generation.
   - Generates reports for financial analysis and strategic decision-making.

3. **Artist Information Management**:
   - Stores detailed artist profiles, including biography, contact information, portfolio, and exhibition history.

4. **Employee Data Management**:
   - Manages employee records, roles, departments, and performance.

5. **Customer Interaction**:
   - Tracks customer inquiries and purchase history for improved engagement and service.

## Database Design
The database design is built around an **Entity-Relationship (ER) Diagram**, which defines the relationships between key entities like artists, artworks, exhibitions, employees, and customers. The design includes the following relationships:

- **Many-to-Many**: Artist-Exhibition, Artwork-Exhibition
- **One-to-Many**: Artist-Artwork, Exhibition-Review, Customer-Purchase
- **One-to-One**: Artwork Purchase-Payment, Payment-Commission

### Relational Schema
The relational schema includes primary and foreign key constraints to ensure data integrity. The following SQL constraints are implemented:
- **PRIMARY KEY**: Ensures uniqueness for each record.
- **FOREIGN KEY**: Maintains referential integrity between related tables.
- **NOT NULL**: Ensures mandatory fields are populated.
- **UNIQUE**: Prevents duplication of certain data (e.g., IDs, phone numbers).
- **CHECK**: Validates data integrity (e.g., rating values within a specific range).

## Database Implementation
The database is implemented using SQL Data Definition Language (DDL) statements. The tables are populated with sample data using **INSERT** statements while maintaining referential integrity.

## Demonstration of Queries
- **Query 1**: Retrieve the top three artists with the most artworks in the gallery.
  ```sql
  SELECT Artist_ID, First_Name, Last_Name, COUNT(Artwork_ID) AS Artwork_Count
  FROM Artist
  JOIN Artwork ON Artist.Artist_ID = Artwork.Artist_ID
  GROUP BY Artist.Artist_ID
  ORDER BY Artwork_Count DESC
  LIMIT 3;

- **Query 2**: Filter exhibitions with an average rating greater than 4.0.
  ```sql
  SELECT Exhibition_ID, Exhibition_Name, AVG(Rating) AS Average_Rating
  FROM Exhibition
  JOIN Exhibition_Review ON Exhibition.Exhibition_ID = Exhibition_Review.Exhibition_ID
  GROUP BY Exhibition_ID
  HAVING AVG(Rating) > 4.0;

- **Trigger**: Automatically calculate and insert commission upon payment.
  ```sql
  CREATE TRIGGER AfterPaymentInsert
  AFTER INSERT ON Payment
  FOR EACH ROW
  BEGIN
    DECLARE commission_rate DECIMAL(5,2);
    DECLARE commission_amount DECIMAL(10,2);
    DECLARE artwork_type VARCHAR(50);
    DECLARE artwork_amount DECIMAL(10,2);

    SELECT Artwork.Type INTO artwork_type FROM Artwork WHERE Artwork.Artwork_ID = NEW.Artwork_ID;
    SELECT Artwork.Amount INTO artwork_amount FROM Artwork WHERE Artwork.Artwork_ID = NEW.Artwork_ID;

    IF artwork_type = 'Painting' THEN
      SET commission_rate = 0.20;
    ELSEIF artwork_type = 'Sculpture' THEN
      SET commission_rate = 0.12;
    ELSE
      SET commission_rate = 0.15;
    END IF;

    SET commission_amount = artwork_amount * commission_rate;

  INSERT INTO Commissions (Payment_ID, Commission_Amount, Commission_Date)
  VALUES (NEW.Payment_ID, commission_amount, NEW.Payment_Date);
  END;

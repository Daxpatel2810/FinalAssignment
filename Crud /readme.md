SENG8071 - Final Assignment

Daksh Patel - 8887759
Chintan Harsora - 8976824
Jaykumar Patel - 8781807

# Group Members and Assigned Duties
---------------------------------------------------------------------
| Member Name    | Student ID | Assigned Duty                        |
|----------------|------------|--------------------------------------|
| Chinatn        | 8976824    | CRUD Operation                       |
| Daksh          | 8887759    | Testing                              |
| Jay            | 8781807    | Environment Setup And Documentation  |
----------------------------------------------------------------------

Tables

# Bookstore System

## Tables and Attributes

### Books
| Attribute Name  | Data Type |
|-----------------|-----------|
| BookId          | 123       |
| Title           | Hopeless  |
| Genre           | Thriller  |
| AuthorId        | HP12      |
| PublisherId     | PB1       |
| PublishDate     | 2008-05-12|
| Price           | 450       |

### Authors
| Attribute Name  | Data Type |
|-----------------|-----------|
| AuthorId        | 452       |
| Name            | J M SOD   |
| Biography       | Time lie  |
| Birthdate       | 1928-09-21|

### BookAuthor
| Attribute Name  | Data Type |
|-----------------|-----------|
| BookId          | 309       |
| AuthorId        | 452       |
| AuthorName      | J M SOD   |

### Publishers
| Attribute Name  | Data Type |
|-----------------|-----------|
| PublisherId     | HJ9       |
| Name            | S D JON   |
| ContactInfo     | 9876789   |

### Customers
| Attribute Name  | Data Type |
|-----------------|-----------|
| CustomerId      | CU2       |
| Name            | BEN WUK   |
| Email           | FA@HAM.   |
| TotalSpent      | $3444     |
| RegistrationDate| 2011-09-11|

### Orders
| Attribute Name  | Data Type |
|-----------------|-----------|
| OrderId         | OD3       |
| CustomerId      | CU2       |
| OrderDate       | 2012-08-14|
| TotalAmount     | $150      |

### OrderDetails
| Attribute Name  | Data Type |
|-----------------|-----------|
| OrderDetailId   | OI9       |
| OrderId         | OD3       |
| BookId          | 378       |
| Quantity        | 1         |
| Price           | $150      |

### Reviews
| Attribute Name  | Data Type |
|-----------------|-----------|
| ReviewId        | RE1       |
| CustomerId      | CU2       |
| BookId          | 378       |
| Rating          | 5         |
| ReviewDate      | 2012-08-15|

## SQL

### Create Tables

```sql
-- Create the Book table
CREATE TABLE `Book` (
    BookID INT PRIMARY KEY,
    Title VARCHAR(20) NOT NULL,
    Genre VARCHAR(10) NOT NULL,
    PublicationDate DATE NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    AverageRating DECIMAL(3, 2),
    PublisherID INT,
    FOREIGN KEY (PublisherID) REFERENCES Publisher(PublisherID)
);

-- Create the Author table
CREATE TABLE `Author` (
    AuthorID INT PRIMARY KEY,
    Name VARCHAR(20) NOT NULL,
    Biography TEXT
);

-- Create the BookAuthor table
CREATE TABLE `BookAuthor` (
    BookID INT,
    AuthorID INT,
    AuthorName VARCHAR(20) NOT NULL,
    PRIMARY KEY (BookID, AuthorID),
    FOREIGN KEY (BookID) REFERENCES Book(BookID),
    FOREIGN KEY (AuthorID) REFERENCES Author(AuthorID)
);

-- Create the Publisher table
CREATE TABLE `Publisher` (
    PublisherID INT PRIMARY KEY,
    Name VARCHAR(20) NOT NULL,
    ContactInfo VARCHAR(20)
);

-- Create the Customer table
CREATE TABLE `Customer` (
    CustomerID INT PRIMARY KEY,
    Name VARCHAR(20) NOT NULL,
    Email VARCHAR(20) NOT NULL UNIQUE,
    RegistrationDate DATE NOT NULL,
    TotalSpentLastYear DECIMAL(10, 2) DEFAULT 0
);

-- Create the Order table
CREATE TABLE `Order` (
    OrderID INT PRIMARY KEY,
    CustomerID INT,
    OrderDate DATETIME NOT NULL,
    TotalAmount DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID)
);

-- Create the OrderItem table
CREATE TABLE `OrderItem` (
    OrderItemID INT PRIMARY KEY,
    OrderID INT,
    BookID INT,
    Quantity INT NOT NULL,
    Price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (OrderID) REFERENCES `Order`(OrderID),
    FOREIGN KEY (BookID) REFERENCES Book(BookID)
);

-- Create the Review table
CREATE TABLE `Review` (
    ReviewID INT PRIMARY KEY,
    BookID INT,
    CustomerID INT,
    Rating INT NOT NULL CHECK (Rating >= 1 AND Rating <= 5),
    ReviewText TEXT,
    ReviewDate DATETIME NOT NULL,
    FOREIGN KEY (BookID) REFERENCES Book(BookID),
    FOREIGN KEY (CustomerID) REFERENCES Customer(CustomerID)
);
 

TYPE-ORM
Table Author

import { Entity, Column, PrimaryGeneratedColumn } from "typeorm";

@Entity()
export class Author {
  @PrimaryGeneratedColumn()
  AuthorID: number;

  @Column({
    length: 20,
  })
  Name: string;

  @Column("text", { nullable: true })
  Biography: string;
}





Table Book
import { Entity, Column, PrimaryGeneratedColumn, ManyToOne } from "typeorm";
import { Publisher } from "./Publisher"; // Ensure you have a Publisher entity defined

@Entity()
export class Book {
  @PrimaryGeneratedColumn()
  BookID: number;

  @Column({
    length: 20,
  })
  Title: string;

  @Column({
    length: 10,
  })
  Genre: string;

  @Column("date")
  PublicationDate: string;

  @Column("decimal", { precision: 10, scale: 2 })
  Price: number;

  @Column("decimal", { precision: 3, scale: 2, nullable: true })
  AverageRating: number;

  @ManyToOne(() => Publisher, (publisher) => publisher.PublisherID)
  Publisher: Publisher;
}



Table BookAuthor
import { Entity, Column, PrimaryColumn, ManyToOne, JoinColumn } from "typeorm";
import { Book } from "./Book";
import { Author } from "./Author";

@Entity()
export class BookAuthor {
  @PrimaryColumn()
  BookID: number;

  @PrimaryColumn()
  AuthorID: number;

  @Column({
    length: 20,
  })
  AuthorName: string;

  @ManyToOne(() => Book)
  @JoinColumn({ name: "BookID" })
  book: Book;

  @ManyToOne(() => Author)
  @JoinColumn({ name: "AuthorID" })
  author: Author;
}




Table Customer
import { Entity, Column, PrimaryColumn } from "typeorm";

@Entity()
export class Customer {
  @PrimaryColumn()
  CustomerID: number;

  @Column({
    length: 20,
  })
  Name: string;

  @Column({
    length: 20,
    unique: true,
  })
  Email: string;

  @Column("date")
  RegistrationDate: string;

  @Column("decimal", { precision: 10, scale: 2, default: 0 })
  TotalSpentLastYear: number;
}



Table Order
import { Entity, Column, PrimaryGeneratedColumn, ManyToOne } from "typeorm";
import { Customer } from "./photo"; // Assume you have a Customer entity

@Entity()
export class Order {
  @PrimaryGeneratedColumn()
  OrderID: number;

  @ManyToOne(() => Customer)
  CustomerID: Customer;

  @Column("datetime")
  OrderDate: Date;

  @Column("decimal", { precision: 10, scale: 2 })
  TotalAmount: number;
}





Table OrderItem
import { Entity, Column, PrimaryGeneratedColumn, ManyToOne } from "typeorm";
import { Order } from "./Order"; // Ensure you have this entity defined
import { Book } from "./Book";   // Ensure you have this entity defined

@Entity()
export class OrderItem {
  @PrimaryGeneratedColumn()
  OrderItemID: number;

  @ManyToOne(() => Order, (order) => order.OrderID)
  Order: Order;

  @ManyToOne(() => Book, (book) => book.BookID)
  Book: Book;

  @Column("int")
  Quantity: number;

  @Column("decimal", { precision: 10, scale: 2 })
  Price: number;
}





Table Publisher
import { Entity, Column, PrimaryGeneratedColumn } from "typeorm";

@Entity()
export class Publisher {
  @PrimaryGeneratedColumn()
  PublisherID: number;

  @Column({
    length: 20,
  })
  Name: string;

  @Column({
    length: 20,
    nullable: true,
  })
  ContactInfo: string;
}




Table Review
import { Entity, Column, PrimaryGeneratedColumn, ManyToOne } from "typeorm";
import { Book } from "./Book"; // Ensure you have this entity defined
import { Customer } from "./Customer"; // Ensure you have this entity defined

@Entity()
export class Review {
  @PrimaryGeneratedColumn()
  ReviewID: number;

  @ManyToOne(() => Book, (book) => book.BookID)
  Book: Book;

  @ManyToOne(() => Customer, (customer) => customer.CustomerID)
  Customer: Customer;

  @Column("int")
  Rating: number;

  @Column("text", { nullable: true })
  ReviewText: string;

  @Column("datetime")
  ReviewDate: Date;
}



CRUD OPERATION
import { Express } from "express";
import { DataSource } from "typeorm";
import { Photo } from "./photo";
import { Author } from './Author';
import express from 'express';
import { createConnection, getRepository } from 'typeorm';
//import { Author } from './Author';
import path from 'path';

export default class PhotoApi {
  #dataSource: DataSource;
  #express: Express;

  constructor(dataSource: DataSource, express: Express) {
    this.#dataSource = dataSource;
    this.#express = express;

    this.#express.get("/Author/:id", async (req, res) => {
      return res.json(
        await this.#dataSource.manager.findBy(Author, {
          AuthorID: parseInt(req.params.id),
        })
      );
    });

    this.#express.post("/Author", async (req, res) => {
      const { body } = req;
      console.log(body);

      const photo = new Author();

      photo.Name = body.Name;
      photo.Biography = body.Biography;
      

      try {
        await this.#dataSource.manager.save(photo);
        console.log(photo has been created with id: ${photo.AuthorID});
      } catch (err) {
        res.status(503);
        return res.json({
          error: "Photo creation failed in db.",
        });
      }

      res.status(200);
      return res.json({
        id: photo.AuthorID,
      });
    });

    this.#express.put("/photo/:id", async (req, res) => {
      const { id } = req.params;
      const { body } = req;
      console.log(body);
    
      const photoId = parseInt(id);
    
      if (isNaN(photoId) || photoId < 0) {
        return res.status(400).json({
          error: "Invalid photo ID.",
        });
      }
    
      try {
        const photo = await this.#dataSource.manager.findOne(Photo, { where: { id: photoId } });
        
        if (!photo) {
          return res.status(404).json({
            error: "Photo not found.",
          });
        }
    
        photo.name = body.name !== undefined ? body.name : photo.name;
        photo.description = body.description !== undefined ? body.description : photo.description;
        photo.filename = body.filename !== undefined ? body.filename : photo.filename;
        photo.views = body.views !== undefined ? body.views : photo.views;
        photo.isPublished = body.isPublished !== undefined ? body.isPublished : photo.isPublished;
    
        await this.#dataSource.manager.save(photo);
        console.log(Photo with id: ${photo.id} has been updated);
        res.status(200).json({
          message: "Photo updated successfully",
          photo: photo
        });
      } catch (err) {
        console.error("Photo update failed:", err);
        res.status(500).json({
          error: "Photo update failed in db.",
        });
      }
    });


this.#express.delete("/photo/:id", async (req, res) => {
  const { id } = req.params;
  const photoId = parseInt(id);

  if (isNaN(photoId) || photoId < 0) {
    return res.status(400).json({
      error: "Invalid photo ID.",
    });
  }

  try {
    const photo = await this.#dataSource.manager.findOne(Photo, { where: { id: photoId } });

    if (!photo) {
      return res.status(404).json({
        error: "Photo not found.",
      });
    }

    await this.#dataSource.manager.remove(photo);
    console.log(Photo with id: ${photo.id} has been deleted);
    res.status(200).json({
      message: "Photo deleted successfully",
    });
  } catch (err) {
    console.error("Photo deletion failed:", err);
    res.status(503).json({
      error: "Photo deletion failed in db.",
    });
     }
      });
  }
}




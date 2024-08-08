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




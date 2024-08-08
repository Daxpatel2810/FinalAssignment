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

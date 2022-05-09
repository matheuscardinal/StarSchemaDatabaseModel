![domain-group-squarelogo-1490584418557](https://user-images.githubusercontent.com/101608594/167379052-b85cef70-6194-4a3a-b40c-4f3f591447d0.png)
# Domain Property of The Week - Database Model

## Business task

### Summary

- Domain launches property of the week which registers users votes on properties.
- Users have one vote per week.
- One property becomes the 'Property of The Week'
- Agency that supplies the property gets the cash prize if the property becomes the property of the week.

### Task

Create a database model (Star/Snowflake schema) which would help to answer the following questions
1. Check if there is double voting by users
2. Check which agency is getting more cash prizes
3. Users from which locations are more involved

## First draft, thoughts and ideas

My first thoughts and ideas were that I would need 4 dimension tables and 1 fact table, which means I was going to create a Star Schema Database Model, and at the same time I was creating the tables on PostgreSQL to test it out and see whether it would work or not.

Reminder: This is the first time I have ever done/studied/touched matters related to database schemas and models.

Here I go.

dimTables:
- Users
- Agency
- Property
- VoteHistory

factTable:
- PropertyWeek

1 - dimUsers
- user_id (PK)
- first_name
- last_name
- email
- user_postcode

```
-- 1. Create Table Statement Users
CREATE TABLE Users (
	user_id serial PRIMARY KEY,
	first_name VARCHAR ( 50 ) NOT NULL,
	last_name VARCHAR ( 50 ) NOT NULL,
	email VARCHAR ( 255 ) UNIQUE NOT NULL,
	user_postcode INTEGER
);

-- 2. Insert Values Statement into Table
INSERT INTO Users VALUES 
(1, 'Elizabeth', 'Tsang', 'elizabethjtsang@gmail.com', 2067),
(2, 'Matheus', 'Cardinal', 'MC@gmail.com', 2050),
(3, 'David', 'Liu', 'davidrfa@gmail.com', 2051),
(4, 'Andre', 'Lee', 'drelee@yahoo.com', 2067),
(5, 'Rafael', 'Rezende', 'rarema@icloud.com', 2065),
(6, 'Gabriela', 'Rinaldi', 'gabi@gmail.com', 2050),
(7, 'Bruno', 'Goncalves', 'bgoncalves@hotmail.com', 2067),
(8, 'Tracy', 'Fox', 'tracy@gmail.com', 2019),
(9, 'Amanda', 'Grilo', 'amandagrilo@bol.com', 2017),
(10, 'Heshan', 'Srilanka', 'hesh@hotmail.com', 2041)
```

2 - dimAgency
- agency_id (PK)
- agency_name

```
-- Create Agency Table
CREATE TABLE Agency (
	agency_id serial PRIMARY KEY,
	agency_name VARCHAR ( 50 ) NOT NULL
);

-- Insert into Agency Table
INSERT INTO Agency VALUES
(11, 'Roses'),
(22, 'Lavender'),
(33, 'Violet')
```

3 - dimProperty
- property_id (PK)
- agency_id (FK)
- street_number
- street_name
- suburb
- postcode

```
-- Create Property table
CREATE TABLE Property (
	property_id serial PRIMARY KEY,
	agency_id INTEGER NOT NULL,
	street_number INTEGER NOT NULL,
	street_name VARCHAR ( 50 ) NOT NULL,
	suburb VARCHAR ( 255 ) UNIQUE NOT NULL,
	postcode INTEGER
);

INSERT INTO Property VALUES
(111, 11, 7, 'Railway Street', 'Chatswood', 2067),
(222, 11, 8, 'Ashley Street', 'Hornsby', 2045),
(333, 22, 9, 'Archer Street', 'Burwood', 2088),
(444, 33, 10, 'Victoria Street', 'Mascot', 2011)
```

4 - dimVoteHistory
- vote_id (PK)
- vote_date
- vote_week
- property_id (FK)
- user_id


```
-- Create Vote Table
CREATE TABLE VoteHistory (
	vote_id serial PRIMARY KEY,
	vote_date DATE NOT NULL,
	vote_week INTEGER NOT NULL, -- remind me to include week condition
	property_id INTEGER NOT NULL,
	user_id INTEGER NOT NULL
);

INSERT INTO VoteHistory VALUES
(1, '2022-01-03', 1, 111, 1),
(2, '2022-01-03', 1, 111, 2),
(3, '2022-01-03', 1, 111, 4),
(4, '2022-01-04', 1, 222, 6),
(5, '2022-01-05', 1, 111, 9),
(6, '2022-01-07', 1, 444, 10),
(7, '2022-01-03', 1, 444, 3),
(8, '2022-01-08', 1, 111, 7),
(9, '2022-01-10', 2, 222, 2),
(10, '2022-01-11', 2, 222, 1),
(11, '2022-01-12', 2, 333, 4),
(12, '2022-01-12', 2, 222, 7),
(13, '2022-01-12', 2, 111, 5),
(14, '2022-01-13', 2, 333, 8),
(15, '2022-01-13', 2, 444, 9),
(16, '2022-01-14', 2, 222, 10),
(17, '2022-01-14', 2, 222, 3),
(18, '2022-01-14', 2, 222, 6)
```

5 - factPropertyWeek
- vote_id
- user_id
- user_postcode
- property_id
- postcode
- vote_week

```
-- Create Property Week Table
CREATE TABLE PropertyWeek (
	vote_id integer references VoteHistory,
	user_id integer references Users,
	user_postcode integer references Users,
	property_id integer references Property,
	postcode integer references Property,
	vote_week integer references VoteHistory,
	agency_id
)
```

### Diagram

My first draft diagram looked like the below

![FirstDraftDiagramStarSchema](https://user-images.githubusercontent.com/101608594/167389877-3cebdd90-708a-403f-8fda-265ce9e0d75c.png)

#### At this exact I stopped because I realised it wouldn't work, I woudln't have been able to query neither connect, least answer the questions. 
### I needed more in depth studies and knowledge.



## Final Diagram - Snowflake Schema Database Model



































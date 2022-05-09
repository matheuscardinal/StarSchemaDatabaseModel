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

#### At this exact moment, I stopped because I realised it wouldn't work, I woudln't have been able to query neither connect, least answer the questions. 
### I needed more in depth studies and knowledge.



## Final Diagram - Star Schema Database Model

After nights awake and a lot of studies I learnt a few things:
- Dimension tables doesn't have Foreinger Keys, they all belong in the fact table;
- Show cardinalities through the lines (I still have to study this more and understand what they properly do);
- Link agency to property, remove user_postcode and agency_id.

And what needs to be changed in my diagram:
- Replace dimVoteHistory and have a dimDate dimension table to list dates and register votes;
- Link the dimVote table to the dimDate, because we need a vote by user being associated to a date;
- Link dimAgency to dimProperty, because each property is associated to an agency;
- Have a fact table as PropertyVotes.

### My final diagram looks like below

![PropertyOfTheWeek](https://user-images.githubusercontent.com/101608594/167426597-863869e8-9a26-45ec-9d32-7425d9264fa2.jpg)

#### To check my fact table is able to answer all 3 questions I created some dummy values to show I can query them.


Here I go.

![CreateFactTable](https://user-images.githubusercontent.com/101608594/167421265-e5146dd5-d09f-46a7-9127-53744727b160.png)

The dummy dataset contains 20 rows, so 20 votes, 8 users, 7 properties, 3 agencies, across 2 weeks (week_date).

Column 'daily_date' for the day that the user voted and 'user_postcode' to find which locations are more involved in the voting.

![FactTable](https://user-images.githubusercontent.com/101608594/167421740-a32195d3-6669-426b-a15d-48f39c80878e.png)

### Answers

#### 1 - Check if there is double voting by users.

![DoubleVote](https://user-images.githubusercontent.com/101608594/167422640-d11d1c9c-b8a0-4f82-91a8-1f17319e683e.png)

Yes, there are double votes by user, as we can see in the screenshot, user 8 has voted several times across 2 weeks, and user 6 in the second week.

#### 2 - Check which agency is getting more cash prizes.

![question2](https://user-images.githubusercontent.com/101608594/167423973-fcdb06ed-59f0-4837-b48a-7517e56b24a5.png)

This query took me hours to figure out! I didn't know how to get the max votes by week and property. Thanks to google, I now know how to use the row_number function to split and rank my rows.

- First I checked which property was the winner, count the votes, so I knew that property 1 from agency 1 wins with 4 votes for the first week, and property 7 from agency 3 wins with 5 votes for the second week. (Please refer to the screenshot above)

- Then once I found out how to split and rank my rows I got to the result that agency 1 wins for the first week and agency 3 wins for the second week. Hypothetically if I had a bigger dataset to work on, I could possibly query and count how many times an agency have won. 

#### 3 - Users from which locations are more involved in the voting.

![question3](https://user-images.githubusercontent.com/101608594/167428158-041a5a31-aa9d-4257-a23b-846e571428ca.png)

As we can see in the screenshot we can have which location is enganging more with the voting. 7 votes from postcode 2033.




























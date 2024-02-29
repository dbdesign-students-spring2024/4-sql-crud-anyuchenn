# Part 1: Restaurant Finder
File: [retaurants.csv](data/restaurants.csv) [Mockaroo](https://mockaroo.com/)
## Table Structure
```sql
CREATE TABLE restaurants (
    id INTEGER PRIMARY KEY,
    category TEXT,
    price_tier TEXT,
    neighborhood TEXT,
    opening_hours TEXT,
    average_rating REAL,
    good_for_kids BOOLEAN
);

CREATE TABLE reviews (
    id INTEGER PRIMARY KEY,
    id INTEGER,
    rating INTEGER,
    review_text TEXT,
    date_posted TEXT,
    FOREIGN KEY (id) REFERENCES restaurants(id)
);

```


## Practice Data
## Import the data into the restaurants table from a CSV file:

```sql
.mode csv
.import /data/users.csv
```

## Queries
## 1.Find the cheap restaurants in a particular NYC neighborhood
```sql
SELECT * FROM restaurants
WHERE price_tier = 'cheap' AND neighborhood = 'Brooklyn';
```

## 2.Find restaurants by Genre with High Rating
```sql
SELECT * FROM restaurants
WHERE category = 'Chinese' AND average_rating >= 3
ORDER BY average_rating DESC;
```

## 3.Find all restaurants that are open now
```sql
SELECT *
FROM restaurants
WHERE substr(opening_hours, 1, 5) <= strftime('%H:%M', 'now', 'localtime') 
AND substr(opening_hours, 7, 5) >= strftime('%H:%M', 'now', 'localtime');
```

## 4.Leave a review for a restaurant
```sql
INSERT INTO reviews (restaurant_id, rating, review_text, date_posted)
VALUES (1, 5, 'Very good!', '2024-02-28');
```

## 5.Delete all restaurants that are not good for kids.
```sql
DELETE FROM restaurants
WHERE good_for_kids = false;

```

## 6.Find the number of restaurants in each NYC neighborhood
```sql
SELECT neighborhood, COUNT(*) AS num_restaurants
FROM restaurants
GROUP BY neighborhood;

```



# Part 2: Social Media App #

Files: [`users.csv`](data/users.csv) [`posts.csv`](data/posts.csv)

```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    email TEXT,
    password TEXT,
    handle TEXT
);

CREATE TABLE posts (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    post_content TEXT,
    post_type TEXT,
    recipient_id INTEGER
    post_visibility BOOLEAN,
    post_timestamp TEXT NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

```sql
.mode csv
.headers on
.import data/users.csv
.import data/posts.csv 
```
## Queries 
## 1. Register a new User
```sql
INSERT INTO users (email, password, handle)
VALUES ('hakdjfk@gmail.com', 'sdhiudh$', 'hskdhjw24');
```
## 2. Create a new Message sent by a particular User to a particular User
```sql
INSERT INTO posts (user_id, post_type, post_content, post_visibility, post_timestamp)
VALUES (1, 'message', 'Hello, this is a message', true, '2024-02-29 08:00:00');
```
## 3. Create a new Story by a particular User
```sql
INSERT INTO posts (user_id, post_type, post_content, post_visibility, post_timestamp)
VALUES (1, 'story', 'This is a story', true, '2024-02-29 08:00:00');
```

## 4. Show the 10 most recent visible Messages and Stories in order of recency
```sql
SELECT *
FROM posts
WHERE post_visibility = true
ORDER BY post_timestamp DESC
LIMIT 10;
```

## 5. Show the 10 most recent visible Messages sent by a particular User to a particular User
```sql
SELECT *
FROM posts
WHERE post_visibility = true
AND post_type = 'message'
AND user_id = sender_user_id
AND recipient_user_id = receiver_user_id
ORDER BY post_timestamp DESC
LIMIT 10;
```


## 6. Make all Stories that are more than 24 hours old invisible.
```sql
UPDATE posts
SET post_visibility = false
WHERE post_type = 'story'
AND (JULIANDAY('now') - JULIANDAY(post_timestamp)) * 24 >= 24;
```

## 7. Show all invisible Messages and Stories, in order of recency
```sql
SELECT *
FROM posts
WHERE post_visibility = false
ORDER BY post_timestamp DESC;
```
## 8. Show the number of posts by each User
```sql
SELECT users.id, users.handle, COUNT(posts.id) AS num_posts
FROM users
LEFT JOIN posts ON users.id = posts.user_id
GROUP BY users.id, users.handle;
```

## 9. Show the post text and email address of all posts and the User who made them within the last 24 hours

```sql
SELECT posts.post_content, users.email
FROM posts
JOIN users ON posts.user_id = users.id
WHERE (JULIANDAY('now') - JULIANDAY(post_timestamp)) * 24 <= 24;
```
## 10. Show the email addresses of all Users who have not posted anything yet.
```sql
SELECT email
FROM users
WHERE id NOT IN (SELECT DISTINCT user_id FROM posts);
```
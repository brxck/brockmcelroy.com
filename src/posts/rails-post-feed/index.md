---
title: 'Creating a Post Feed with Active Record'
date: 2018-01-28
tags: [Ruby, Rails, Web]
---

In my latest app I'm trying to create a post feed that displays a user's friends' posts along with their own posts. I did a lot of searching for the best way to do this and came up with a lot of ugly answers.

Many people suggest creating an array of IDs from the user's friends and selecting posts with matching IDs. A better solution might be to use subselects, and in fact, this is what I learned in Michael Hartl's RailsTutorial:

```ruby
friend_ids = "SELECT friend_id FROM friendships
  WHERE user_id = :user_id"
Post.where("user_id IN (#{friend_ids})
  OR user_id = :user_id", user_id: id)
```

Both of these solutions require explicitly telling Active Record the SQL query to execute, which seems to defeat the purpose of Active Record.
This is because there seems to be no way to combine Active Record results into a single object in the same way "OR" would in SQL.

That is until Rails 5 introduced the `.or` method, which has yet to appear in any of the Rails Guides. Check this out:

```ruby
@posts = Post.where(user: current_user)
  .or(Post.where(user: current_user.friends))
```

The feed generation is now done entirely through Active Record and its associations. It looks a lot cleaner too!

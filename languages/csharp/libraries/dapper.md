# Dapper

## Split on
- sql querries just return tabular data, so how does dapper know to structure the data as one to one, one to many, or many to many relationships in c# objects?
- this is where the `splitOn` parameter comes in
- consider the following classes
    ```cs
    public class Tag
    {
        public int TagId { get; set; }
        public string TagName { get; set; }
        public List<Post> Posts { get; set; }
    }
    public class Post
    {
        public int PostId { get; set; }
        public string Headline { get; set; }
        public string Content { get; set; }
        public Author Author { get; set; }
        public List<Tag> Tags { get; set; } 
    }
    ```
- and then this sql
    ```sql
    select p.postid, headline, t.tagid, tagname
    from posts p 
    inner join posttags pt on pt.postid = p.postid
    inner join tags t on t.tagid = pt.tagid
    ```
- this is how you would return a list of posts with their tags
    ```cs
    using (var connection = new SQLiteConnection(connString))
    {
        var sql = @"select p.postid, headline, t.tagid, tagname
                    from posts p 
                    inner join posttags pt on pt.postid = p.postid
                    inner join tags t on t.tagid = pt.tagid";
        var posts = await connection.QueryAsync<Post, Tag, Post>(sql, (post, tag) => {
            // this lambda is what dapper uses to construct the objects
            post.Tags.Add(tag);
            return post;
        }, splitOn: "tagid" /* this is what tells dapper where one object stops and where the next one begins */);
        var result = posts.GroupBy(p => p.PostId).Select(g =>
        {
            var groupedPost = g.First();
            groupedPost.Tags = g.Select(p => p.Tags.Single()).ToList();
            return groupedPost;
        });
        foreach(var post in result)
        {
            Console.Write($"{post.Headline}: ");
            foreach(var tag in post.Tags)
            {
                Console.Write($" {tag.TagName} ");
            }
            Console.Write(Environment.NewLine);
        }
    }
    ```
- consider this sql
    ```sql
    select p.postid, headline, firstname, lastname, t.tagid, tagname
    from posts p 
    inner join authors a on p.authorid = a.authorid
    inner join posttags pt on pt.postid = p.postid
    inner join tags t on t.tagid = pt.tagid
    ```
- this is how you would put the data together in c#
    ```cs
    using (var connection = new SQLiteConnection(connString))
    {
        var sql = @"select p.postid, headline, firstname, lastname, t.tagid, tagname
                    from posts p 
                    inner join authors a on p.authorid = a.authorid
                    inner join posttags pt on pt.postid = p.postid
                    inner join tags t on t.tagid = pt.tagid";
        var posts = await connection.QueryAsync<Post, Author, Tag, Post>(sql, (post, author, tag) => {
            post.Author = author;
            post.Tags.Add(tag);
            return post;
        }, splitOn: "firstname, tagid");
        var result = posts.GroupBy(p => p.PostId).Select(g =>
        {
            var groupedPost = g.First();
            groupedPost.Tags = g.Select(p => p.Tags.Single()).ToList();
            return groupedPost;
        });
        foreach(var post in result)
        {
            Console.Write($"{post.Headline}: ");
            foreach(var tag in post.Tags)
            {
                Console.Write($" {tag.TagName} ");
            }
            Console.Write($" by {post.Author.FirstName} {post.Author.LastName} {Environment.NewLine}");
        }
    }
    ```
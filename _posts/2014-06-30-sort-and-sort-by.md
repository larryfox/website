---
title: sort and sort_by
date: 2014-06-29 20:00:00 -04:00
---

A quick brief of Ruby’s sorting algorithm.

~~~ruby
@count = 0
an_array = [].fill(0...1000) { rand(1000) }

an_array.sort do |a, b|
  @count += 1
  a <=> b
end

@count # => 8982
~~~

The above sorts a random 1000 element array, calling its block 8982 times. That exact count will vary, but since Ruby uses [quicksort](https://en.wikipedia.org/wiki/Quicksort) under the hood, we can expect *O(n log n)* comparisons for an array of length *n*. With that in mind let’s imagine that we need to sort an array based on a property of its elements. For example, to sort email addresses by TLD we might code this up.

~~~ruby
def extract_tld(email)
  @count += 1
  email.split('.').last
end

emails = %w[
  lisa@example.net
  bill@example.com
  sara@example.info
  jack@example.us
  jill@example.org
]

emails.sort do |a, b|
  extract_tld(a) <=> extract_tld(b)
end
~~~

This works, and will probably get the job done, but how many times does our inconspicuous method get called on this tiny 5 element array?

~~~ruby
@count # => 14
~~~

No surprise here, `#extract_tld` is getting called twice for each comparison, which means some email addresses are getting computed more that once.

This example is trivial but with larger data sets and more expensive operations this problem can balloon. We can fix this by doing some preprocessing on our input, namely using the [Schwartzian Transform](https://en.wikipedia.org/wiki/Schwartzian_transform). The basic idea is this:

1. Map over the input, and return a 2 element array, consisting of a preprocessed sorting key, and the original value. So `[item_1, … item_n]` becomes `[[key_1, item_1], … [key_n, item_n]]`.
2. Perform sort based on the first element of the sub arrays.
3. Map over the array again, reversing the effect of step 1.

Translated to Ruby, and annotated to display what's going on:

~~~ruby
emails
        # =>["lisa@example.net", "bill@example.com", "sara@example.info", "jack@example.us", "jill@example.org"]
  .map { |x| [extract_tld(x), x] }
        # => [["net", "lisa@example.net"], ["com", "bill@example.com"], ["info", "sara@example.info"], ["us", "jack@example.us"], ["org", "jill@example.org"]]
  .sort
        # => [["com", "bill@example.com"], ["info", "sara@example.info"], ["net", "lisa@example.net"], ["org", "jill@example.org"], ["us", "jack@example.us"]]
  .map { |x| x[1] }
        # => ["bill@example.com", "sara@example.info", "lisa@example.net", "jill@example.org", "jack@example.us"]

@count # => 5
~~~

Now instead of *O(n log n)* calls to `#extract_tld`, we're doing exactly *n* of them, in this case 5. This concept is so useful that Ruby actually has it built in (and implemented in C). It's what `#sort_by` does in the background. So the above could even be shortened to this more readable one liner:

~~~ruby
emails.sort_by { |x| extract_tld(x) }
@count # => 5
~~~

Note that `#sort` is generally faster for simple comparisons, and `#sort_by` for more complex comparisons; as with most things, choose the right tool for the job. The Ruby docs for [`#sort_by`](http://www.ruby-doc.org/core-2.1.2/Enumerable.html#method-i-sort_by) touch on this a bit more. Benchmarking [with examples](https://gist.github.com/larryfox/b999ef8484e5a2e61024) from this post I get the following:

~~~json
Comparison:
            sort_by:     1108.4 i/s
       map.sort.map:      323.2 i/s - 3.43x slower
               sort:      283.8 i/s - 3.91x slower
~~~
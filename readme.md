# Lite ActiveRecord

Custom implemented Lite version of ActiveRecord.

## Technologies

- SQLite3
- Ruby

Utilized meta programming to implement the basic features of ActiveRecord using only Ruby and Sqlite3.


```ruby

module Searchable
  def where(params)
    where_line = params.map { |k, v| "#{k} = ?" }.map(&:to_sym).join(' and ')
    results = DBConnection.execute(<<-SQL, *params.values)
    select
      *
    from
      #{self.table_name}
    where
      #{where_line}
    SQL
    parse_all(results)
  end
end


def belongs_to(name, options = {})
   self.assoc_options[name] = BelongsToOptions.new(name, options)

   define_method(name) do
     options = self.class.assoc_options[name]
     f_key = self.send(options.foreign_key)
     p_key = options.primary_key
     options.model_class.where(p_key => f_key).first
   end


 end

 def has_many(name, options = {})
   self.assoc_options[name] = HasManyOptions.new(name, self.to_s, options)

   define_method(name) do
     options = self.class.assoc_options[name]
     p_key = self.send(options.primary_key)
     f_key = options.foreign_key

     options.model_class.where(f_key => p_key)
   end
 end
```

# class_eval v.s. instance_eval 

* Use `MyClass.class_eval` to define a instance method of `MyClass` 
* Use `MyClass.instance_eval` to define a class method of `MyClass` 

--- 
## instance_eval: Getting instance variable from object 
Remeber everyhing in ruby is object. A Ruby object contains a hashtable of instance variable, `klass/super` pointers and flags. 

Let's look at this class 
```Ruby
class People
  def initialize(name)
      @name 
  end   
end

lucas = People.new('lucas') 
```

Now if I want access the `@name` of `lucas`, I can't just do `lucas.name`, as I don't have reader method for `@name` defined

But what I can do is 
```Ruby 
lucas.instance_eval { @name } # => 'lucas'
```
What is does is, find the `@name` instance variable from `lucas` object, and return it 

---
## instance_eval: Define class method  

Let's make this a bit more complicated 
```Ruby 
lucas.instance_eval do
  def name 
    @name 
  end
end 

lucas.name # => 'lucas'
People.new.name # => undefined method 'name'  
```
Now you are adding a method to access the instance variable of `lucas` object. 
Where should that method live ? It can not live in the same object where `@name` lives, hence, can not live in `lucas`. In fact, it lives in the singleton classs of `lucas`. 
That's why when you created a different `People` instance, it won't have access to `name` method.  


Ok, now let's look at this code 
```Ruby
People.instance_eval do 
  def hi
    put 'hi'
  end
end

People.new.hi # => `method not defined` 
People.hi # => 'hi'
```
Remeber everyhing in Ruby is a object. Class `People` is also an object/instance of class `Class`. 
When you do `People.instance_eval`, you are adding a method to `People`'s singleton class. Hence, the method become a class method of `People` class.  

---
## What does class_eval do

`class_eval` add a method to the calling object(`self`), the caller must be a class object. 
How ruby knows that? As we mentioned eariler, ruby object use flags to determine the object type. 

Let's back on our previous example of  `People` 
Now I want to make all `people` be able to expose their name, What I can do ?
```
People.class_eval do
  def name 
    @name 
  end
end 
```
It actually add the `name` method to the class Object `People`, not the singleton class of `People`. 
Hence, it become an instance method in `People` class. 

# Environments
Alathea  
2014-07-21  



***

## Exercises


***

## Reading Notes

### Quiz

#### List three ways that an environment is different to a list.

> * Every object in an environment has a unique name.
> * The objects in an environment are not ordered (i.e. it doesn’t make sense to ask what the first object in an environment is).
> * An environment has a parent.
> * Environments have reference semantics.

#### What is the parent of the global environment? What is the only environment that doesn’t have a parent?

The `empty` environment has no parent.

#### What is the enclosing environment of a function? Why is it important?

#### How do you determine the environment from which a function was called?

#### How are `<-` and `<<-` different?

`<-` sets a value in the current environment. `<<-` sets the value in all environments (i think)

### Environment basics

Variables can point to objects that are equivalent but stored in different places in the environment.  This could be a cause of reduced performance if they are pointing to two different giant objects.

Objects that have lost their name are automatically deleted by the garbage collector.



***

## Discussion Notes

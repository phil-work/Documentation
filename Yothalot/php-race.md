# Yothalot\Race

Yothalot was designed to run map/reduce jobs. However, since Yothalot
had to be able distribute jobs over different servers for that, we
decided to also support different types of jobs: like race jobs.

The *Yothalot\Race* class offers you the possibility to start a number of
parallel running PHP scripts. The result of the first job to
complete is returned back to you. This could for example be useful if you
try to locate information in a large set of log files -- as soon as one
job finds the appropriate entry in the log the result is returned an all
other jobs are stopped.

## Yothalot\Race interface

To write a race job, you have to create a class that implements this
Yothalot\Race interface. This interface looks as follows:

```php
<?php
interface Yothalot\Race
{
    public function includes();
    public function process($value);
}
?>
```
When you write your own race class, keep in mind that the Yothalot
framework distributes the job over multiple servers. It is therefore possible
that your object gets serialized and is moved to a different server, and
that multiple instances are running at the same time.


## Serializing and unserializing

The Yothalot framework serializes and unserializes your objects to distribute them
over different nodes in the cluster. If there are any files that have to be
included before the object is unserialized, you can name these files in the
`includes()` method.

```php
<?php
class MyRace implements Yothalot\Race
{
    /**
     *  Files that should be loaded before the object is unserialized
     *  @return string[]
     */
    public function includes()
    {
        return array(__FILE__);
    }
    
    // @todo implement the other methods
}
?>
```

PHP classes are serializable by default. If you however want to write your own
custom serialize and unserialize algorithm, you can simply implement the
[Serializable interface](http://php.net/manual/en/class.serializable.php):

```php
<?php
class MyRace implements Yothalot\Race, Serializable
{
    /**
     *  Files that should be loaded before the object is unserialized
     *  @return string[]
     */
    public function includes()
    {
        return array(__FILE__);
    }

    /**
     *  Serialize the object into a string value
     *  @return string
     */
    public function serialize()
    {
        // @todo add your implementation
    }
    
    /**
     *  Counter part of the resize() method: turn a string back into an object
     *  @param  string
     */
    public function unserialize($input)
    {
        // @todo add your implementation
    }
    
    // @todo implement other methods from the 
}
?>
```

## Processing

The final method that should be implemented is the `process()` method. In this
method you implement your data processing algorithm. The method receives
one parameter, the data, and should return NULL if the algorithm was not completed
(the job did not win the race), or a non NULL value if the algorithm is won.

```php
<?php
class MyRace implements Yothalot\Race
{
    /**
     *  Implementation for a process function
     *  @param  mixed       Value that is being mapped
     *  @return mixed       Your return value
     */
    public function process($value)
    {
        // does this job win the race?
        if (check_if_data_contains_what_we_were_looking_for($value))
        {
            // return the found data (all other running sub-jobs will be killed)
            return "data found";
        }
        else
        {
            // data was not found, let other processes continue
            return NULL;
        }
    }
}
?>
```


# Rows and Columns

Produce ad-hoc reports from `.csv` files

## Origin

I was chopping some data out of some `.csv` files for the umteenth time and finally got distracted enough to create the tool that I had always wanted

## Warnings

As this tool is just for me there are some _limitations_ that might irk you. So far this meets my needs and the motivation to fix them is rather low

* The data has to be `tab` seperated. I know the files it processes are `.csv` and should support at least the `comma` but it works for me
* You can only give a single column despite being able to supply multiple rows. My reports are already wide enough as they are
* I am a programmer so the first column in the file is refered to as `0` and the second as `1` etc
* There is very little error checking :(
* Assumes that all numbers are floating point

## Usage

```shell
$ rc --rows 0 --column 1 --value 2 sales.csv
```

* **`--rows`** is a required option and should be a list of columns that will be combined to create the rows of the report
* **`--column`** is an optional column that specifies the column that will make up the columns of the report
* **`--value`** is the optional column (assumed to be a number of some sort) that will be accumulated

Some examples will make things clear :)

## Examples

The `sales.csv` file contains some example data. Assume it is a list of sales from various towns by region. The first column is the region, the second the town and the third column the value of the sale

|0|1|2|
|---|---|---|
|Scotland|Aberdeen|3|
|Wales|Swansea|3|
|Ireland|Cork|1|
|Scotland|Glasgow|1|
|Scotland|Edinburgh|4|
|England|Bath|3|
|England|Bath|3|
|England|London|3|
|...|...|...|

```shell
$ rc --rows 0 sales.csv
```

Will produce the following output...

```
          :    **blank**
----------+-------------
England   :        31.00
Ireland   :        21.00
Scotland  :        24.00
Wales     :        24.00
----------+-------------
**total** :       100.00
```

We only gave a single row so this is just a count of the number of records. We can read this that there were 31 sales in England, 21 in Ireland etc.

```shell
$ rc --rows 0 --value 2 sales.csv
```

Instead of counting the number of records we have now summed the sales for each region.

```
          :    **blank**
----------+-------------
England   :        93.00
Ireland   :        54.00
Scotland  :        75.00
Wales     :        71.00
----------+-------------
**total** :       293.00
```

Now we will have a look how the `--column` option changes things

```shell
$ rc --rows 0 --column 1 sales.csv
```

Gives (in slightly abbreviated form) the same report as our first example but with values at the town level. Given that Aberdeen is not in England cells showing `-.--` have no data rather than a value of `0.00`. If we add the `--value 2` option then we would get the values of the sales by town

```
          :     Aberdeen :         Bath :       Brecon :      ...     :    **total**
----------+--------------+--------------+--------------+--------------+-------------
England   :         -.-- :         6.00 :         -.-- :      ...     :        31.00
Ireland   :         -.-- :         -.-- :         -.-- :      ...     :        21.00
Scotland  :         6.00 :         -.-- :         -.-- :      ...     :        24.00
Wales     :         -.-- :         -.-- :         6.00 :      ...     :        24.00
----------+--------------+--------------+--------------+--------------+-------------
**total** :         6.00 :         6.00 :         6.00 :      ...     :       100.00
```

A final couple of examples

```shell
$ rc --rows 0,1 sales.csv
```

The rows are now the combination of `Region - Town`. You can have as many columns make up a row as you want. I find this format easier to process than the insanely wide report above

```
                     :    **blank**
---------------------+-------------
England - Bath       :         6.00
England - Hull       :         6.00
England - London     :        10.00
England - Manchester :         9.00
Ireland - Cork       :         8.00
Ireland - Dublin     :         2.00
Ireland - Limerick   :         9.00
Ireland - Westport   :         2.00
Scotland - Aberdeen  :         6.00
Scotland - Edinburgh :         9.00
Scotland - Glasgow   :         6.00
Scotland - Perth     :         3.00
Wales - Brecon       :         6.00
Wales - Cardiff      :         6.00
Wales - Newport      :         5.00
Wales - Swansea      :         7.00
---------------------+-------------
**total**            :       100.00
```

Finally

```shell
$ rc --rows 1,0 sales.csv
```

The same report but ordered by `Town - Region`

```
                     :    **blank**
---------------------+-------------
Aberdeen - Scotland  :         6.00
Bath - England       :         6.00
Brecon - Wales       :         6.00
Cardiff - Wales      :         6.00
Cork - Ireland       :         8.00
Dublin - Ireland     :         2.00
Edinburgh - Scotland :         9.00
Glasgow - Scotland   :         6.00
Hull - England       :         6.00
Limerick - Ireland   :         9.00
London - England     :        10.00
Manchester - England :         9.00
Newport - Wales      :         5.00
Perth - Scotland     :         3.00
Swansea - Wales      :         7.00
Westport - Ireland   :         2.00
---------------------+-------------
**total**            :       100.00
```

# Thats all folks

Hope you find it useful

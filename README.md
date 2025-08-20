# graphtail
[![Build](https://github.com/demogorgon1/graphtail/actions/workflows/cmake.yml/badge.svg)](https://github.com/demogorgon1/graphtail/actions/workflows/cmake.yml)

You've got a program that outputs a bunch of numbers to a CSV file? You want to visualize those numbers with graphs 
in real-time, while the program is still running and appending new numbers to the file?
If you can say yes to both those questions, _graphtail_ might be the tool you need.

![test4](https://user-images.githubusercontent.com/7039567/216010144-ea1f3a02-7bfb-458b-9fbe-73104bd3e721.gif)

## Installing
### From source
First of all you'll need git and cmake to acquire and build the project. Then you can run:
```
git clone https://github.com/demogorgon1/graphtail.git
cd graphtail
mkdir build
cd build
cmake ..
cmake --build .
```
On Windows, if successful, you'll find ```graphtail.exe``` in ```src\Debug```.
On Linux (and similar), if successful, you can run ```make install``` to install ```graphtail```.

### Downloading binaries
For Windows you can download the zip'd .exe file available under "Releases".

## Usage
```
graphtail [options] <input files>
```
Renders numeric data from input CSV files into a window. Any changes to the files will automatically update the window.

Option|Description
-|-
```--row_delim=<character>```| Character used as row deliminator in CSV files or ```new_line```. Defaults to ```new_line```.
```--column_delim=<character>```| Character used as column deliminator in CSV files. Defaults to ```;```.
```--width=<width>```<br>```--height=<height>```| Sets the size of the window. Defaults to a 1000x500.
```--font_size=<size>```| Sets the size of the font used to display information. Defaults to 14.
```--x_step=<pixels>```| Instead of stretching graph to fit the width of the window, each data point will advance the specified number of pixels the x-axis. This option can be used in a group definition.
```--y_min=<min>```<br>```--y_max=<min>```| Clamp the graph y-axis to the specified range. Default is to stretch. This option can be used in a group definition.
```--histogram_threshold=<value>```| Histogram values must be higher than this to be rendered. Default is to not have a threshold. This option can be used in a group definition.
```--is_size```| Numbers will be shown with K/M/G suffixes if large enough. This option can be used in a group definition.
```--groups=<definition>```| Defines graph groups. See example below. If no groups are defined, all columns will get their own group automatically.
```--config=<path>```| Loads configuration from specified file. See below for an example of a configuration file.

## Keyboard shortcuts
Key|Action
-|-
ESC|Terminate program.
F1|Toggle x-axis stretching to fit window width.

## Group definitions
All graphs will be shown separately per default, but you can group specific ones together if you want to with the ```--groups``` option.

Group description syntax looks a bit wonky because it's easy to parse, but it's quite simple:

```{``` marks the beginning of a group and ```}``` ends it. Inside the brackets you can use ```i(column)``` to added ```column``` to the group. ```column``` can also be a wildcard (for example ```*something*```), which will cause any column with a name matching the wildcard to be added to the group. Inside the group you can also specify group-specific parameters with ```!option=value```. You can see which options can be specified per group in the list above.
Use ```h(name)(column1, column2, ...)``` to turn the group into a histogram heatmap.

### Example 1

![example1](https://user-images.githubusercontent.com/7039567/216309538-c6c89bf0-3584-4a7d-8733-97b90be9ba78.png)

```
--groups={i(foo)i(bar)!y_min=0!y_max=1}{i(baz)}
```

This will cause the columns ```foo``` and ```bar``` to be added to the same group and the y-axis will be clamped between 0 and 1. The column ```baz``` will be put in a separate group.

### Example 2

![example2](https://user-images.githubusercontent.com/7039567/216310991-768b63ec-21e5-449c-aba1-6d8f42171c67.png)

```
--groups={h(foo)(foo1,foo2,foo3,foo4)!histogram_threshold=0}
```

Render columns ```foo1```, ```foo2```, ```foo3```, and ```foo4``` as a histogram heatmap named ```foo```. Cells of the heatmap must have a value of at least 0 to be rendered.

## Configuration files
A configuration file is a list of statements:

```
input /path/to/some/csv-file
width 500
height 500
groups {i(foo)i(bar)}
```

Alternatively you can specify values like this:

```
begin groups
  {
    i(foo)
    i(bar)
  }
end
```
  
This is particularily useful if you have a lot of groups and you want your configuration to be more readable.


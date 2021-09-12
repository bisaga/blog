---
layout: post
title: "Ada vs Java vs C#"
date: "2016-08-14"
categories: 
  - "ada"
  - "c"
  - "java"
tags: 
  - "ada"
  - "c"
  - "java"
---

# Ada vs Java vs C#

## Performance measurement sample

I prepare small and simple problem just for measuring real life performance of different run-time environments and corresponding languages.  For now I implemented three versions: Ada, C# and Java.

The code use: object instance creation, parameter passing, returning a value from function, simple string manipulation and simple arithmetic operation. Sample was running for 50 million times in the loop. Each measurement represent one full execution of the main program.  My environment was:  Windows 10 64bit, Intel Core i7 2630QM processor and 8 GB RAM.

I am not sure how is that even possible, but **Java** was 3 times faster then C# and 10 times faster then Ada, it's a **clear winner** here !

Looks like compiled language (Ada) lost this battle with highly optimized virtual machines, especially with Java HotSpot VM, which also compile some parts of java code to machine code too.

### Measurements in seconds

[![2016-08-14 23_43_47-Ada_vs_Java_vs_C#.odt - LibreOffice Writer](/assets/images/2016-08-14-23_43_47-Ada_vs_Java_vs_C.odt-LibreOffice-Writer-300x170.png)](http://bisaga.com/blog/wp-content/uploads/2016/08/2016-08-14-23_43_47-Ada_vs_Java_vs_C.odt-LibreOffice-Writer.png)(Lower is better)

### Ada code

GPS 2016 (20160515) hosted on **i686-pc-mingw32** GNAT GPL 2016 (20160515-49) (c) 2001-2016 AdaCore

Compiled with **full optimizations**, without debugging:

gprbuild -d -PH:\\Ada\\SOURCE\\Codesample1\\codesample1.gpr gcc -c -gnat12 -O3 -gnatp main.adb gcc -c -gnat12 -O3 -gnatp calculators.adb gprbind main.bexch gnatbind main.ali gcc -c b\_\_main.adb gcc main.o -o main.exe

with Ada; use Ada;
with Ada.Strings.Unbounded; use Ada.Strings.Unbounded;
with Ada.Calendar; use Ada.Calendar;
with Ada.Text\_IO;

with Calculators;

procedure Main is
   Count  : Integer;
   Start\_Time: Ada.Calendar.Time;
   Max : Integer;
begin
   Start\_Time := Ada.Calendar.Clock;
   Count  := 0;
   Max := 50\_000\_000;
   for I in Integer range 1..Max loop
      Count := Calculators.Calculate(Count => Count, Max => Max);
   end loop;
    
   Ada.Text\_IO.Put\_Line("Time: " & Duration'Image(Ada.Calendar.Clock - Start\_Time) & " s");
end Main;

with Ada.Strings.Unbounded; use Ada.Strings.Unbounded;

package Calculators is

   type Calculator is tagged
      record
         Result: Integer;
         Name: Unbounded\_String;
      end record;

   function Calculate(This: in out Calculator; Count: Integer; Max: Integer) return Integer;

   function Calculate(Count: Integer; Max: Integer) return Integer;

end Calculators;

with Ada.Strings.Unbounded; use Ada.Strings.Unbounded;
with Ada.Text\_IO;

package body Calculators is

   function Calculate(This: in out Calculator;  Count: in Integer; Max: Integer) return Integer is
   begin
      This.Result := Count + 1;
      This.Name := To\_Unbounded\_String("Name:") & This.Result'Image;
      if This.Result = Max then
        Ada.Text\_IO.Put\_Line(To\_String(This.Name));
      end if;
      return This.Result;
   end Calculate;
   
   
   -- create new instance for each call without pointers (Calculator'Access, new and Unchecked\_Deallocation)  
   function Calculate(Count: in Integer; Max: Integer) return Integer is
      Calc: Calculator;
      Result : Integer;
   begin
      Result := Calc.Calculate(Count, Max);
      Return Result;
   end Calculate;

end Calculators;

### C# code

Microsoft Visual C# 2015 Microsoft .Net Framework version 4.6.01038

Running »Release build«

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Diagnostics;

namespace SimpleMeasureTestCSharp
{
    class Program
    {
        static void Main(string\[\] args)
        {
            Stopwatch stopwatch = new Stopwatch();
            stopwatch.Start();
            int max = 50000000;
            int count = 0;
            Console.WriteLine("Start");
            for (int i = 1; i <= max; i++)
            {
                Calculator c = new Calculator();
                count = c.calculate(count, max);
            }
            long elapsedMilliseconds = stopwatch.ElapsedMilliseconds;
            float seconds = (float) elapsedMilliseconds / 1000;
            Console.WriteLine("Time: " + seconds.ToString() + " s");
            Console.ReadKey();
        }
    }
}

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace SimpleMeasureTestCSharp
{
    class Calculator
    {
        private int Result;
        private String Name;

        public int calculate(int count, int max)
        {
            Result = count + 1;
            Name = "Result: " + Result.ToString();
            if (Result == max)
            {
                Console.WriteLine(Name);
            }
            return Result;
        }
    }

### Java code

**Java runtime:** java version "1.8.0\_60" Java(TM) SE Runtime Environment (build 1.8.0\_60-b27) Java HotSpot(TM) 64-Bit Server VM (build 25.60-b23, mixed mode)

package com.company;
public class Main {
    public static void main(String\[\] args) {
        long start\_time = System.currentTimeMillis();
        int count = 0;
        int max = 50000000;
        for (int i = 1; i <= max; i++) {
            Calculator c = new Calculator();
            count = c.calculate(count, max);
        }
        long end\_time = System.currentTimeMillis();
        long diff\_time = end\_time - start\_time;
        float seconds = (float)diff\_time/1000;
        System.out.println("Time: "  + seconds + " s");
    }
}

package com.company;
/\*\*
 \* Created by igorb on 14.8.2016.
 \*/
public class Calculator {
    private int Result;
    private String Name;
    public int calculate(int count, int max)
    {
        Result = count + 1;
        Name = "Result: " + Result;
        if(Result == max) {
            System.out.println(Name);
        }
       return Result;
    }
}

## TypeScript/JavaScript

Out of pure curiosity I wrote the same test with typescript. The results from running under node  (on the same machine) are:

igorb@PAVILION MINGW64 /c/Bisaga/Workspaces/learning/calcspeed
$ node ./dist/index.js
Start.
Result: 50000000
Time: 7.456 sec

igorb@PAVILION MINGW64 /c/Bisaga/Workspaces/learning/calcspeed
$ node ./dist/index.js
Start.
Result: 50000000
Time: 7.709 sec

igorb@PAVILION MINGW64 /c/Bisaga/Workspaces/learning/calcspeed
$ node ./dist/index.js
Start.
Result: 50000000
Time: 7.529 sec

igorb@PAVILION MINGW64 /c/Bisaga/Workspaces/learning/calcspeed
$ node ./dist/index.js
Start.
Result: 50000000
Time: 7.358 sec

igorb@PAVILION MINGW64 /c/Bisaga/Workspaces/learning/calcspeed
$ node ./dist/index.js
Start.
Result: 50000000
Time: 7.387 sec

igorb@PAVILION MINGW64 /c/Bisaga/Workspaces/learning/calcspeed
$ node ./dist/index.js
Start.
Result: 50000000
Time: 7.496 sec

Really great news is that javascript runtime under node / V8 chrome machine,  is such a great performer. With average of **7.489 sec** is totally on pair with other strong typing languages.

Code is written with typescript 2.5.2, compiled to "es2017" javascript standard and run under node v8.4.0. The hardware/OS environment is the same as above.

**The code in typescript:**

export class Calculator {
    private name: string = "";
    private result: number = 0;

    public calculate(count: number, max: number): number {
        this.result = count + 1;
        this.name = "Result: " + this.result;
        if (this.result === max) {
            console.log(this.name);
        }
        return this.result;
    }
}

import {Calculator} from "./calculator";

function main() {
    const startDt: Date = new Date();

    console.log("Start.");
    let count: number = 0;
    const max: number = 50000000;

    for (let index = 0; index < max; index++) {
        const calc = new Calculator();
        count = calc.calculate(count, max);
    }

    const endDt: Date = new Date();
    const diff = endDt.valueOf() - startDt.valueOf();
    console.info("Time: %d sec", diff / 1000);
}

main();

###### Conclusion

I know those tests doesn't prove anything really. But when I wrote code it is written without deep understanding of internal optimization algorithms inside runtime engines. So it is important to me which runtime will help me most without huge investment of my time.

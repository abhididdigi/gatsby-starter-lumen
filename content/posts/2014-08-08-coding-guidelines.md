---
layout: post
title: Coding Guidelines
--- 



 {{page.title}}
======================================================





A coding style is much needed before we start writing any line of code. This coding style should be followed in all of the Scripts/Packages/Utilities that we write. This is not the final copy of this, as we become more aware of Javascript and it's properties, this document is likely to change.

I am a big fan of Mozilla [coding style](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Coding_Style).

_Some key guidelines:_

1. Always include license information before writing any utility. Standard License that should be used is the MIT license <sup><a href="#fn1" id="ref1">1</a></sup> , for sharing code on ServiceNow community.
There is really no reason as to include the licensing information if the piece of code belongs to a particular Client you are working for. Henceforth the License information is optional. But please make sure you include License towards your company and yourself, if you were to share it on [ServiceNow Share](www.share.servicenow.com)


2. Always follow Java Doc block comments. Getting some inspiration from there, and Stripping it of the useless tags, we will be using @author, @version, @param, @return, @exception.

  Please note, this should follow the above exact order.

3. Always, the default indentation is two spaces. Don't use a tab, because tab has different meaning under different environments.
 
4. Always when defining an array, use `var arr = [] `, please don't use `var arr = new Array()`. Similarly, when defining an Object, please use `var obj = {"key":"value",...}` instead of the `new` key word. Also if you know that `a` is Boolean, the comparison should be `if(a)` instead of `if( a==true)`. Meaning when comparing a Boolean parameter, always use unary operator or no operator.

5. Don't leave `gs.log` , `gs.print` lying around. `gs.log` will lead to performance issues, and leaving `gs.print` will make the code look less elegant.

6. Script Includes always start with capital letter. They are equivalent to Classes in Java, and Javascript largely follows the code guideliness of Java. Functions and methods should start with small letter. Camel case is preferred. When writing functions in Business Rules/Client Scripts, please follow the below guidelines.


      a) In any Business Rule or Client Script, nest the code inside a function. Never leave any code not inside a function, because Javascript does a terrible job of moving your code to Global scope.

      b) When the code is nested, instead of

             
                    callMe(a,b,c)
                    function callMe(a,b,c){
                          //your code here.
                     }
 
              
              
      
              
    use self invoking pattern, this will make  the  code  look more elegant like this      
          
             
                ( function callMe(a,b,c){
                        //your code here
                  })(a,b,c);

             
7. Don't leave local variables sprayed all along the code. Define all the local variables, at the starting line of the code.

<hr></hr>

<sup id="fn1">1. There are many websites to get a copy of MIT License. I suggest you to a copy from http://opensource.org/licenses/MIT .<a href="#ref1" title="MIT License">↩</a></sup>



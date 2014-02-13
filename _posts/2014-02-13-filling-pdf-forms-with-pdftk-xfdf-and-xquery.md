---
published: true
layout: post
title: Filling PDF forms with PDFtk, XFDF, and XQuery
tags: 
  - XQuery
  - eXist-db
  - PDF
  - PDFtk
  - XFDF
---

It's hard to believe that PDF is still used for forms these days; they seem so last decade, or even century, compared to what's possible on the web. I dread the tedium and boredom of filling out a PDF form with tons of fields or creating multiple editions of a form with different information on each. 

To illustrate the pain of a PDF form, imagine you have a single blank form—take NARA's [SF702](http://www.archives.gov/isoo/security-forms/sf702.pdf) form. This is a good example, because it contains so many fields. Some of the fields appear to be intended to be filled out with a pen, but it would save time if you could pre-populate the form with the days of each month. But a year's worth of forms would mean 365 fields. Tab through the form, noting how the cursor jumps through the fields out of order. Entering these forms with a program like Adobe Acrobat is an RSI-inducing nightmare. You'd be tempted to give up and use a pen, which rather defeats the purpose of an electronic form. 

What if you could generate and organize your data first and then have a program do the tedious form filling for you? You might know how to generate dates with a spreadsheet or with a programming language like XQuery. But how can you get data onto the form? How can you automate the task of filling in the data? Here's how.

The key resource that got me started was a very helpful article called ["Fill Online PDF Forms"](http://www.mactech.com/articles/mactech/Vol.20/20.11/FillOnlinePDFFormsUsingHTML/index.html) by Sid Steward. The article introduced Sid's tool, [PDFtk](http://www.pdflabs.com/tools/pdftk-server/)—a free command line tool for manipulating PDF files—and showed how to extract information about the fields in a PDF form and use this information to generate a Forms Data Format (FDF) file with your data and apply it to the PDF.  I downloaded the installer for my Mac (it works on Windows and Linux too), installed it, and got to work extracting the fields from [my PDF form](http://www.archives.gov/isoo/security-forms/sf702.pdf), `sf702.pdf`, with this Terminal command:

`$ pdftk sf702.pdf dump_data_fields > sf702-data-fields.txt`

This command tells PDFtk to look for the data fields—the text boxes or other input widgets on the form—from the PDF and "dump" them into a new file, `sf702-data-fields.txt`.

If you look at the data fields file, you'll see a series of entries like this, one per data field that PDFtk identified:

    ---
    FieldType: Text
    FieldName: form1[0].#subform[0].MONTHYEAR[0]
    FieldNameAlt: MONTH/YEAR. Enter 2 digit month and 2 digit year.
    FieldFlags: 8388608
    FieldJustification: Left
    ---

Of these five entries per field, the key entry for our purposes is the second one, `FieldName`. You'll use `FieldName` to tell PDFtk which field to insert each piece of your data into.  (I'd suggest that you compare the order of the entries in the data fields file with the order of the fields in the original form by pressing the `tab` key in your PDF viewer to advance from field to field; the order should be the same, which is quite handy when finding poorly named field names.)  

As Sid's article explains, once you know the `FieldName` values for your form, you then need to generate a Forms Data Format (FDF) file that contains the values for each field you want to fill in on the form. An FDF file is just a text file that a program like PDFtk can use to fill your data into the correct field. So what does FDF look like? Sid provided the following example, with two fields (`city` and `state`) and two corresponding values to fill in (`San Francisco` and `California`):

    %FDF-1.2
    %aaIO
    1 0 obj
    << /FDF << /Fields [	<< /T (city)  /V (San Francisco) >>
                                     << /T (state) /V (California) >> ] >>
    >>
    endobj
    trailer << /Root 1 0 R >>
    %%EOF

Yuck! A nasty, domain-specific format! Sid kindly provided a PHP-based `forge_fdf` script to ease the pain of creating an FDF file, but I didn't want to muck with PHP; nothing against PHP, but I wanted to use XQuery, the programming language I happen to know best. I knew I could write a script to generate the calendar and other data, but the sample FDF looked awful, and the thought of generating FDF with XQuery nearly made me give up. 

As one final effort, I searched Google for "FDF XML" and was excited to find an XML edition of FDF, called [XFDF](http://en.wikipedia.org/wiki/Portable_Document_Format#XML_Forms_Data_Format_.28XFDF.29), the *XML* Forms Data Format.  Instead of needing to generate something like the FDF sample above, I just needed a nice, clean XML document in the XFDF namespace, containing `field` elements and a simple name-value structure like this:

{% highlight xml %}
<xfdf xmlns="http://ns.adobe.com/xfdf/">
    <fields>
        <field name="city">
            <value>San Francisco</value>
        </field>
        <field name="state">
            <value>California</value>
        </field>
    </fields>
</xfdf>
{% endhighlight %}

Anyone who has used XML could easily code this by hand, but if you add a little XQuery knowledge, you could dynamically generate fields and values easily. And best of all, Sid's PDFtk tool is just as happy to take XFDF and plain old FDF and apply it to your form.

So I set about creating my XFDF files. I fired up eXist-db, started its built-in XQuery editor, [eXide](https://github.com/wolfgangmm/eXide) ([live demo](http://exist-db.org/exist/apps/eXide)). Knowing that I would need calendaring functions for my form, I used eXist-db's Package Manager to install the [FunctX library](http://www.xqueryfunctions.com/), a library with useful functions, including `days-in-month()`. Since I needed 12 forms, one for each month in the year, I wrote an XQuery that generated 12 separate XFDF documents. You can see [my full XQuery](https://gist.github.com/joewiz/8970317) for [my specific form](http://www.archives.gov/isoo/security-forms/sf702.pdf), but I present a slightly simplified version here:

{% highlight xquery %}
xquery version "3.0";

import module namespace functx="http://www.functx.com";

let $year := 2014
    (: Generate the months of year as padded, two-digit values, e.g., January > 01 :)
let $months := (1 to 12) ! functx:pad-integer-to-length(., 2) 
for $month in $months
    (: Craft an xs:date for each month to calculate the days in the month :)
let $first-day := xs:date(string-join(($year, $month, '01'), '-')) 
let $days := (1 to functx:days-in-month($first-day)) ! functx:pad-integer-to-length(., 2)
let $month-year := $month || '/' || substring($year, 3, 2)
return
    <xfdf xmlns="http://ns.adobe.com/xfdf/" xml:space="preserve">
        <fields>
            <field name="form1[0].#subform[0].MONTHYEAR[0]">
                <value>{$month-year}</value>
            </field>
            { 
            for $day at $n in $days 
            let $field-name :=
                if ($n le 22) then 
                    'form1[0].#subform[0].Date' || $n || '[0]'
                else 
                    'form1[0].#subform[0].Date' || $n - 22 || '[1]'
            return
                <field name="{$field-name}">
                    <value>{$day}</value>
                </field>
            }
        </fields>
    </xfdf>
{% endhighlight %}

This script generates 12 XFDF elements, one for each month of the year, with fields for month-year (e.g., `01/14` for January 2014) and for each day of the month (e.g., `01`-`22` for the first column of fields containing 22 days of the month, and `23` forward for the second column containing the remaining days of the month).  Notice also that I had to go through effort to generate the correct field names (e.g., `form1[0].#subform[0].Date1[0]` for the field containing the first day of the month).  These were the field names as I found them in the text file of field data that I generated above with PDFtk. Although the field names were complex, at least there was a pattern that I could use to generate them without typing each one.

Once I had my XQuery working correctly, I copied the resulting XFDF documents out of the database onto disk and crafted a bash script to apply the XFDF files onto the form with PDFtk:

{% highlight bash %}
$ for FDF in *.xml; do BASENAME=`basename -s .xml $FDF`; pdftk sf702.pdf fill_form "$FDF" output $BASENAME.pdf; done
{% endhighlight %}

This takes each XFDF file (e.g., `sf702-01.xml` with the January form), applies the file to the blank PDF form (`sf702.pdf`), and saves the filled form as a new PDF using the basename of the XFDF file (`sf702-01`) plus the `.pdf` prefix to yield the `sf702-01.pdf` file for January.

It worked like a charm!

To illustrate some other capabilities of PDFtk, I merged the PDFs together to form a single file for convenience:

{% highlight bash %}
$ pdftk *.pdf cat output sf702-2014.pdf
{% endhighlight %}

And I "flattened" the form data to save space (over 60% in my case), which also makes the form no longer editable:

{% highlight bash %}
$ pdftk sf702-2014.pdf output sf702-2014-flattened.pdf flatten
{% endhighlight %}
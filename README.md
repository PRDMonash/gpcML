
# gpcML
**gpcML** is an XML-based data standard for data generated via gel permeation chromatography
It is intended to fix the issues surrounding data sharing by creating a standardised structure 
which users can store and share experimental data.

## Scope
Currently the standard only works for SEC/GPC setups which utilise an RI detector, work is still in 
progress for expanding the types of detectors which can be stored.

This standard works for any GPC/SEC data which utilises an RI detector.

**The data standard contains:**
| Data Type | Description |  
| ----------- | ----------- |  
| Summary Values | Contains all values which can describe the polymer such as the Mn and Mw. |  
| Contact List | Contains information such as the names associated with the data file and optional contact information. |
| Metadata | • Date (YYYY-MM-DD) <br> • Method Name <br> • Calibration Name <br> • Operator <br> • Instrument Name <br> • Run Time (hh:mm:ss) <br> • Last Service Date (YYYY-MM-DD) <br> • Sample Name <br> • Observational Data <br> Also contains additional list entry for string representation of polymer sample, formats currently accepted are [pSMILES](https://psmiles.readthedocs.io/en/latest/), [BigSMILES](https://olsenlabmit.github.io/BigSMILES/docs/line_notation.html#the-bigsmiles-line-notation) and [G-BigSMILES](https://doi.org/10.1039/d3dd00147d)|
| Instrument Info | This contains areas for users to store information such as serial numbers, manufacturers and names of experimental equipment used to analyse the polymer. The current iteration stores detector and column identities.|
| Runtime Data | **Instrument Configuration** stores the flow rate, column setup, column attributes (pore sizes), temperature (^o^C), injection volume (µL), eluent attributes in addition to whether recycling mode was on and what additive was used in what concentration. <br> <br> **Raw data** is stored as a base64 encoded string which minimises the chance of modifying the data post-analysis. The underlying data can technically be in any format, but a future update will rectify this as it is still being discussed as to what the best way to represent the raw data is. <br> <br> **Integration Values** Stores the volume values where the baseline area is, where peaks are present (which can be many), and the solvent peaks (which can be many).|
| Calibration Data | Contains information relating to the calibration used for the SEC/GPC, this includes the coefficients for the fit, what degree of fit was used, and the MHKS values of the polymer if known |

It is important to note that ***some***  data values within the standard are non-optional and other optional.

## Quick-Start
The data standard comes in the form of the file labeled [gpcML.xsd](https://github.com/PRDMonash/gpcML/blob/main/gpcML.xsd), here the file can be downloaded to see the code that defines the structure. Additionally, there is the documentation pdf that defines all the elements, attributes and complex types used in the data standard. It also visually shows the structural relationships of each element which can help locate data within the file.

### Python code to extract data

Below is some code to read a gpcML formatted XML file in python, this code shows an example file "sample.xml" having it's raw base64 coded string extracted from the file and decoded into it's resulting string.
```python
import xml.etree.ElementTree as ET
import base64

root = ET.parse("sample.xml").getroot()
raw_data = root.find("RunTimeData/RawData")

data = base64.b64decode(raw_data.text)
print(data)
```
Here, root is an ElementTree object that then has multiple methods which can be used to extract various pieces of information, the docs can be viewed [here](https://docs.python.org/3/library/xml.etree.elementtree.html). Then, the find() method is used to access a specific location in the object in a similar fashion to a file explorer. Finally, the text from the "RawData" element is called with **raw_data.text** which then can be decoded back from base64 into it's original text.
### Validation
Validation can be done either online or locally. 

Validation works by ensuring the structure of the created xml document meets the specification outlined in the xsd, which also asserts whether the data type of the stored values match those outlined in the xsd.

**Online**
1. Visit the website [gpcml.com](gpcml.com)
2. On the validator tab, click upload file
3. upload the desired xml file to validate
4. click validate

**Local**
ensure you have the most up to date version of gpcML downloaded and saved in the same place that python is being run. Also ensure you have installed the most up to date version of lxml with the pip command.
```bash
pip install lxml
```
Then, you need to load the schema using the second line here, load the xml file using the third line, and finally use the assertValid method to return whether the document passes the validation. 
```python
from lxml import etree

schema = etree.XMLSchema(etree.parse("gpcML.xsd"))
document = etree.parse("sample.xml")

schema.assertValid(document)
print("XML is valid")
```
If the document fails validation, then the assertValid method will return an error describing where validation failed.
## Data Model
All relationships can be found within the documentation PDF. You can locate the element of interest by clicking on it in the table of contents.


## Current version
the most upto date version of this standard is 1.0

## Tools
Currently the only tool that supports gpcML is www.gpcML.com, this allows you to validate, extract some information, and quickly plot the various distributions of a analysed polymer sample. If you are interested in creating a tool for gpcML or are a vendor looking to create an export filter, please reach out to Tanja.Junkers@monash.edu.

## Contributing

Feedback and contributions to gpcML are welcome.

### Suggesting changes

Please open a GitHub Issue to report a problem, ask a question, or propose
a change to the standard. Include:

- The element or section affected.
- The proposed change and why it is needed.
- An example or use case, where possible.
- Any impact on compatibility with existing gpcML files.

## Licence

The gpcML XML schema and source code, including code examples in the
documentation, are licensed under the [MIT License](LICENSE-MIT).

The written specification and documentation, excluding code examples,
are licensed under [CC BY 4.0](LICENSE-CC-BY-4.0).

These licences do not impose a licence on independently created
experimental datasets merely because they use the gpcML format.



This repository contains models that were created in the context of research works on formal rules- + AI-based inconsistency detection and correction in SysML models.
Most of its content comes from our the repository where we stored the companion data for our first works on the topic: https://github.com/zebradile/ttool-ai

# Setup and Usage Instructions

## 1. Install TTool  
First, install **TTool** from [https://ttool.telecom-paris.fr](https://ttool.telecom-paris.fr).
The recommended method is to download and compile it from its public GitLab repository:
[https://gitlab.telecom-paris.fr/mbe-tools/TTool](https://gitlab.telecom-paris.fr/mbe-tools/TTool).
Detailed installation and configuration instructions are available here:
[https://ttool.telecom-paris.fr/installation.html](https://ttool.telecom-paris.fr/installation.html)

---

## 2. Configure TTool-AI  
Once TTool is installed, configure **TTool-AI** as described here:
[https://ttool.telecom-paris.fr/ttoolai.html](https://ttool.telecom-paris.fr/ttoolai.html).
You will need either an **OpenAI** or **MistralAI** API key, or a locally hosted LLM.

---

# LLM-based inconsistency detection and correction

- Go to "LLMApproach" directory
- There, there are 3 .xml files to be opened with TTool. Do configure TTool as explained before in the "model generation" section.

## Model content
 Each file contains 2 block diagrams (BD) and 2 Use case diagrams (UCD) automatically generated using the system specification. They also contain the  models corrected thansk to the detection of incoherencies. These incoherencies are given in the updated models.

## How to reproduce the results?

Let us assume that you have opened with TTool a model, for instance spacebasedystem.xml. We assume that TTool is correcly configured to use ChatGPT.

### Detecting incoherencies between diagrams

Select a block diagram (BD1 or BD2), make a right click, "to textual format". Do the same for UCD1 or UCD2.

Now, open the AI window (use the icon on the right depicting a brain), then select "Identify incoherencies". In the question box, put:
- Specification: the specification of the system.
- Diagram1: Use case diagram. There, open the UCD in the modeling window, and in the AI window, click on "Append current diagram text".
- Diagram2: Block diagram. There, open the BD in the modeling window, and in the AI window, click on "Append current diagram text".

The question box should now contain the system specification, then the textual description of the UCD, then the textual description of the BD. Now, click on "start". If TTool was able to exchange with ChatGPT, then you should obtain a list of incoherencies, that could look like this:

```
AI: json
{
  "incoherencies": [
    {
      "diagram": "Diagram1",
      "description": "There is no actor or use case for ErrorCorrectionCode, which is a significant part of the system as per the specification."
    },
    {
      "diagram": "Diagram1",
      "description": "Use case 'Monitor_Safety_Data' is shown to be associated with RF_Receiver, which is incorrect as monitoring is a ground station activity."
    },
    {
      "diagram": "Diagram2",
      "description": "Block 'GroundStation' has attributes for temperature, batteryLevel, and fuelQuantity, which are not its attributes but those of the space-based system."
    },
    {
      "diagram": "Diagram2",
      "description": "Signal 'tM_7978(int value)' in RFTransmitter and its corresponding relation are unaccounted for in Diagram1."
    },
    {
      "diagram": "Diagram2",
      "description": "Signal 'memoryWriteCRC()' and 'memoryReadCRC()' in 'ErrorCorrectionCode' block have no corresponding use cases or activities in Diagram1."
    },
    {
      "diagram": "Diagram2",
      "description": "There is no block representing the Watchdog's activity of checking if tasks are still responsive, which is a critical system function as per the specification."
    },
    {
      "diagram": "Diagram2",
      "description": "There is no representation for the cancellation and recomputation of TM upon receiving another TC, as described in the specification."
    },
    {
      "diagram": "Diagram2",
      "description": "The association of 'Microcontroller' with the task of restarting the system if tasks are unresponsive is not clearly translated into relationships with other blocks."
    }
  ]
}
```

### Updating diagrams from inconsistencies

Imagine that you now wish to update the block diagram considering these inconsistencies. Open the AI window of TTool. Select at the top "system blocks". In the question field, put:
- "Specification:" followed with the system specification
- "Diagram1, use case diagram:" followed with the textual description of your selected UCD
- "Diagram2, block diagram:" followed with the textual description of your selected BD
- the following text, taken from incoherencies:
```
Do correct the block diagram considering the following incoherencies:
1. Block 'GroundStation' has attributes for temperature, batteryLevel, and fuelQuantity, which are not its attributes but those of the space-based system
2. Signal 'tM_7978(int value)' in RFTransmitter and its corresponding relation are unaccounted for in Diagram1
3. There is no block representing the Watchdog's activity of checking if tasks are still responsive, which is a critical system function as per the specification
4. There is no representation for the cancellation and recomputation of TM upon receiving another TC, as described in the specification
5. The association of 'Microcontroller' with the task of restarting the system if tasks are unresponsive is not clearly translated into relationships with other blocks
Do correct incoherency 1-5 to propose a new block diagram.

```
Click on start, wait. Then click on apply, and reorganize the blocks: you should have an updated Block Diagram.


---

# Graph-based inconsistency detection

- Go to "DependencyGraphApproach" directory
- There, there is one .xml file to be opened with TTool

## Model content

The file contains a UCD; four BD: M\_complete, M\_incomplete and M\_faulty, and an alternative version of M\_incomplete; and the dependency graph of M\_complete.

## Computing inconsistencies

In TTool command-line interface (in your terminal), type the following command:
```
a cc -12 -nodouble -exact UCD M_incomplete
```

This will output the list of logical dependency inconsistencies detected in M\_incomplete with respect with UCD:
```
9 inconsistencies found:
	In UCD: Element with value "getSensorsData" has no correspondence in M_incomplete
	In UCD: Element with value "Anemometer" has no correspondence in M_incomplete
	In UCD: Element "getSensorsData" logically depends on "IMU" but not in M_incomplete
	In UCD: Element "getSensorsData" logically depends on "Anemometer" but not in M_incomplete
	In UCD: Element "getSensorsData" logically depends on "GNSSReceiver" but not in M_incomplete
	In UCD: Element "regulatePositionAndHeading" logically depends on "IMU" but not in M_incomplete
	In UCD: Element "regulatePositionAndHeading" logically depends on "getSensorsData" but not in M_incomplete
	In UCD: Element "regulatePositionAndHeading" logically depends on "Anemometer" but not in M_incomplete
	In UCD: Element "regulatePositionAndHeading" logically depends on "GNSSReceiver" but not in M_incomplete

```

---


Enjoy!

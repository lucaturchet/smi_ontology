# Competencey questions for the Smart Musical Instruments ontology

The given SPARQL are _examples_ that may be reinterpreted and reused for applications. You can run these queries on the example_dataset.ttl, which represents knowledge related to the Sensus Smart Guitar developed by company Elk.

Prefixes:

```
PREFIX ex: <http://www.example.audio#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX smi: <http://purl.org/ontology/iomust/smi#> 
PREFIX iot: <http://purl.org/ontology/iomust/internet_of_things/> 
PREFIX iomust: <http://purl.org/ontology/iomust/internet_of_things/iomust> 
PREFIX sosa: <http://www.w3.org/ns/sosa/> 
PREFIX studio: <http://purl.org/ontology/studio/> 
PREFIX mixer: <http://purl.org/ontology/studio/mixer/> 
PREFIX device: <http://purl.org/ontology/studio/device/> 
PREFIX sigproc: <http://purl.org/ontology/spd/sigproc/> 
PREFIX fx: <https://w3id.org/aufx/ontology/1.0#> 
PREFIX microphone: <http://purl.org/ontology/studio/microphone/> 
PREFIX multitrack: <http://purl.org/ontology/studio/multitrack#> 
PREFIX connectivity: <http://purl.org/ontology/studio/connectivity#> 
PREFIX mo: <http://purl.org/ontology/mo/> 
PREFIX af: <https://w3id.org/afo/onto/1.1#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/> 
PREFIX event: <http://purl.org/NET/c4dm/event.owl#>
PREFIX timeline: <http://purl.org/NET/c4dm/timeline.owl#>
```

1. Which type of sensors and actuators compose the Sensus Smart Guitar?
```
SELECT DISTINCT ?audioSensor ?audioSensorType ?gestureSensor ?gestureSensorType ?audioActuator ?audioActuatorType ?hapticActuator ?hapticActuatorType ?visualActuator ?visualActuatorType
WHERE { 
  ?instrument				rdfs:label 			"Sensus Smart Guitar" .
  {
    ?instrument				smi:smi_component	?audioInputInterface .
	?audioInputInterface	rdf:type			smi:AudioInputInterface .
	?audioInputInterface	sosa:hosts			?audioSensor . 
  	?audioSensor			rdf:type			?audioSensorType .
    ?audioSensorType		rdfs:subClassOf 	microphone:Microphone .
  } 
  UNION
  {
    ?instrument				smi:smi_component	?GestureInterface .
	?GestureInterface		rdf:type			smi:GestureInterface .
	?GestureInterface		sosa:hosts			?gestureSensor . 
	?gestureSensor			rdf:type			?gestureSensorType .
  	?gestureSensorType		rdfs:subClassOf		smi:GestureTrackingSensor .
  }  
  UNION 
  {
    ?instrument				smi:smi_component	?SoundDeliverySystem .
    ?soundDeliverySystem	rdf:type			smi:SoundDeliverySystem .
	?soundDeliverySystem	sosa:hosts			?audioActuator . 
	?audioActuator			rdf:type			?audioActuatorType .
    ?audioActuatorType 		rdfs:subClassOf		smi:AudioActuator
  }
  UNION 
  {
    ?instrument				smi:smi_component	?HapticDeliverySystem .
    ?HapticDeliverySystem	rdf:type			smi:HapticDeliverySystem .
	?HapticDeliverySystem	sosa:hosts			?hapticActuator . 
	?hapticActuator			rdf:type			?hapticActuatorType .
    ?hapticActuatorType 	rdfs:subClassOf		smi:HapticActuator 
  }
  UNION 
  {
    ?instrument				smi:smi_component	?VisualDeliverySystem .
    ?VisualDeliverySystem	rdf:type			smi:HapticDeliverySystem .
	?VisualDeliverySystem	sosa:hosts			?visualActuator . 
	?visualActuator			rdf:type			?visualActuatorType.
    ?visualActuatorType 	rdfs:subClassOf		smi:VisualActuator 
  }
}
```

2. How many smart guitars are equipped with a piezoelectric microphone system?
```
SELECT COUNT(DISTINCT ?instrument)
	WHERE {
		?instrument				rdf:type			smi:SmartGuitar .  
		?instrument				smi:smi_component	?audioInputInterface .
		?audioInputInterface	rdf:type			smi:AudioInputInterface .
		?audioInputInterface	sosa:hosts			?microphone . 
		?microphone				rdf:type			microphone:PiezoelectricMicrophone .
}
```

3. Which synthesizers are used in the sound engine of the Sensus Smart Guitar?
```
SELECT  ?synthesizer 
	WHERE {
		?instrument				rdfs:label 			"Sensus Smart Guitar" . 
		?instrument				smi:smi_component	?soundEngine .
		?soundEngine			rdf:type			smi:SoundEngine .
		?soundEngine 			device:component	?softwareMixer .
		?softwareMixer			rdf:type			mixer:SoftwareMixer .
		?softwareMixer			mixer:channel		?channel .
		?channel				smi:hosts_plugin	?plugin .
		?plugin					rdf:type			fx:PlugIn .
		?plugin					smi:plugin_type		?synthesizer .
		?synthesizer 			rdf:type			smi:Synthesizer .
	}
```

4. Which audio files and which MIDI scores are associated respectively to backing tracks and MIDI tracks in the sound engine of the Sensus Smart Guitar?
```
SELECT DISTINCT ?audioFileLabel ?midiScoreLabel
	WHERE {
		?instrument						rdf:type 				smi:SmartGuitar . 
		?instrument						rdfs:label 				"Sensus Smart Guitar" . 
		?instrument						smi:smi_component		?soundEngine .
		?soundEngine					rdf:type				smi:SoundEngine .
		?soundEngine 					device:component		?SoftwareMultitrackProject .
		?SoftwareMultitrackProject 		rdf:type				smi:SoftwareMultitrackProject .
		{
			?SoftwareMultitrackProject		multitrack:track		?audioTrack .
			?audioTrack 					rdf:type				multitrack:AudioTrack .
			?audioTrack						multitrack:clip			?audioFile .
			?audioFile						rdf:type				multitrack:AudioClip .
			?audioFile						rdfs:label				?audioFileLabel .
		}
		UNION {
			?SoftwareMultitrackProject		multitrack:track		?MIDITrack .
			?MIDITrack						rdf:type				multitrack:MidiTrack .
			?MIDITrack						smi:midi_score			?midiScore .
			?midiScore						rdf:type				smi:MIDIScore .
			?midiScore						rdfs:label				?midiScoreLabel .
		}
}
```

5. How many smart guitars are using the RetrologueSynth audio plugin associated to a pressure sensor in the sensor interface?
```
SELECT (COUNT(DISTINCT ?instrument) as ?count)
	WHERE {
		?instrument				rdf:type 				smi:SmartGuitar . 
		?instrument				smi:smi_component		?soundEngine .
	    ?instrument				smi:smi_component		?GestureInterface .
		?GestureInterface		rdf:type				smi:GestureInterface .
		?GestureInterface		sosa:hosts				?gestureSensor . 
	  	?gestureSensor			rdf:type				smi:PressureSensor .
		?soundEngine			rdf:type				smi:SoundEngine .
		?soundEngine 			device:component		?softwareMixer .
		?softwareMixer			rdf:type				mixer:SoftwareMixer .
		?softwareMixer			mixer:channel			?channel .
		?channel				smi:hosts_plugin		?plugin .
		?plugin					rdf:type				fx:PlugIn .
		?plugin					rdfs:label				"RetrologueSynth" .
		?plugin					fx:has_parameter		?parameter .	
		?gestureSensor			smi:mapping_function	?transform .
		?transform				rdf:type				smi:MappingTransform .
		?transform				smi:maps				?parameter .
	}


```


6. Which services and applications are available for smart guitars and what are their purposes?
```
SELECT ?instrument ?service ?servicePuropose ?application ?applicationPuropose
	WHERE {
		?instrument				rdf:type 					smi:SmartGuitar . 
		{
		?instrument				smi:smi_service				?service .
		?service				smi:smi_service_purpose		?servicePuropose .
		}
		UNION 
		{ 
			?instrument			smi:smi_application			?application .
			?application		smi:smi_application_purpose	?applicationPuropose .
		}
}
```

7. Which pieces of stage equipment are connected to a smart piano at minute 12:00 during the concert?
```
SELECT ?instrument ?stageEquipment
	WHERE {
		?instrument				rdf:type 									smi:SmartGuitar . 
		?instrument				rdfs:label 									"Sensus Smart Guitar" . 
		?instrument				iomust:hasConnectionWith					?stageEquipment .
		?stageEquipment 		rdf:type									iomust:StageEquipment .	
        ?stageEquipment			iot:isInvolvedIn 							?concert .
     	?concert 				iot:produces/event:time/timeline:starts 	"12:00 min" .	
}
```

8. What SMIs are connected to smartphones at minute 5 during the concert?
```
SELECT DISTINCT ?instrument ?instrumentType
	WHERE {
		?instrument				rdf:type 									iomust:SmartInstrument . 
		?instrument				rdf:type									?instrumentType .
		?instrumentType			rdfs:subClassOf								iomust:SmartInstrument .
		?instrument				iomust:hasConnectionWith					?smartphone .
		?smartphone 			rdf:type									iomust:Smartphone .	
        ?instrument				iot:isInvolvedIn 							?concert .
		?smartphone				iot:isInvolvedIn 							?concert .
     	?concert 				iot:produces/event:time/timeline:starts 	"5:00 min" .	
}                                                                       	
```

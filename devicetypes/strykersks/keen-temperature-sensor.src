/**
 *  Keen Temperature Sensor
 *
 *  Copyright 2018 Sean Schneyer
 *
 *  Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except
 *  in compliance with the License. You may obtain a copy of the License at:
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 *  Unless required by applicable law or agreed to in writing, software distributed under the License is distributed
 *  on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License
 *  for the specific language governing permissions and limitations under the License.
 *
 */
 
import physicalgraph.zigbee.clusters.iaszone.ZoneStatus
import physicalgraph.zigbee.zcl.DataType
 
metadata {
	definition (name: "Keen Temperature Sensor", namespace: "StrykerSKS", author: "Sean Schneyer") {
		capability "Relative Humidity Measurement"
		capability "Temperature Measurement"
        capability "Battery"
        capability "Refresh"
        fingerprint profileId: "0104", deviceId: "0302", inClusters: "0000,0003,0001,0020", outClusters: "0019", manufacturer:"LUMI", model:"RS-THP-MP-1.0", deviceJoinName: "Keen Temperature Sensor"

	}


	simulator {
		// TODO: define status and reply messages here
	}

	tiles {
		// TODO: define your main and details tiles here
	}
}

// parse events into attributes
def parse(String description) {
	log.debug "Parsing '${description}'"
	// TODO: handle 'humidity' attribute
	// TODO: handle 'temperature' attribute
    // TODO: handle 'battery' attribute

    def map = zigbee.getEvent(description)
    
    if(!map){
    	log.debug "No map created..."
		if (description?.startsWith('read attr')) {			
        	log.debug "Starts with read attr"
			map = batteryHandler(description)
		}
	}

    if(map) {
        sendEvent(map)
		log.debug "Parsed (result): '${map}'"
    } else {
        // zigbee.getEvent was unable to parse description. Description must be parsed manually.
        log.debug "Unable to parse description."
    }
}


def batteryHandler(String description){
	def descMap = zigbee.parseDescriptionAsMap(description)
    log.debug "descMap: ${descMap}"
    
	def map = [:]
    
    if (descMap.clusterInt == 0x0403 && descMap.attrInt == 0x0020) {
                map = getBatteryResult(zigbee.convertHexToInt(descMap.value))
	}
            
	if (descMap?.clusterInt == 0x0403 && descMap.attrInt == 0x0020) {
		map = getBatteryPercentageResult(Integer.parseInt(descMap.value, 16))
	}

return map
}

private Map getBatteryResult(rawValue) {
    log.debug "Battery: ${rawValue}"
    def volts = rawValue / 10
    if (volts > 3.0 || volts == 0 || rawValue == 0xFF) {
        return [:]
    }
    else {
        def result = [
                name: 'battery'
        ]
        def minVolts = 2.1
        def maxVolts = 3.0
        def pct = (volts - minVolts) / (maxVolts - minVolts)
        result.value = Math.min(100, (int)(pct * 100))
        def linkText = getLinkText(device)
        result.descriptionText = "${linkText} battery was ${result.value}%"
        
        return result
    }
}


def getBatteryPercentageResult(rawValue) {
	log.debug "Battery Percentage rawValue = ${rawValue} -> ${rawValue / 2}%"
	def result = [:]
	if (0 <= rawValue && rawValue <= 200) {
		result.name = 'battery'
		result.translatable = true
		result.value = Math.round(rawValue / 2)
		result.descriptionText = "${device.displayName} battery was ${result.value}%"
	}
	return result
}


# Summary
This page is used to document changes to the caseUI for the SOC.

### Descriptions
+ caseui.xml –  Define the visible tabs, fields and field types.
+ case.properties - Allows custom fields to be used in arcsight event auditing
+ resource_strings.properties – Defines names & dropdown items
+ label_strings.properties  - Define tab labels

### Locations
|File|Resource|Directory|
|---|---|---|
|caseui.xml|ESM|/opt/arcsight/manager/config/caseui.xml|
|case.properties|ESM|/opt/arcsight/manager/config/audit/case.properties|
|case.default.properties|ESM|/opt/arcsight/manager/config/audit/case.default.properties|
|label_strings.properties|ESM|/opt/arcsight/manager/i18n/common/label_strings.properties|
|resource_strings_en.properties|ESM|/opt/arcsight/manager/i18n/common/resource_strings.properties|
|resource_strings_en.properties|Console|/arcsight/console/current/i18n/common/resource_strings.properties|
|label_strings_en.properties|Console|/arcsight/console/current/i18n/common/label_strings.properties|

### Caseui.xml
![alt text](https://github.com/Ayyyyyyyy/arcsight/blob/master/img/caseui.png "caseui.png")

### Code
```xml
<!--
 * Custom CaseUI File
 * Created by: Matt D
 * V 1.3
 -->
<!-- New Case Layout -->
<editor enforceLocking="true" colorTreeBy="stage" width="480" height="480">
      <tab name="cases.tab.attributes" type="base" showExport="true">
          <component name="attributesTable" type="table">
              <parameter name="cases.header.case" type="header"/>
                <parameter name="name" type="resourceName"/>
                <parameter name="stage" type="stringList"/>
                <parameter name="detectionTime" type="string" readOnly="true"/>
                <parameter name="estimatedStartTime" type="string" readOnly="true"/>
                <parameter name="estimatedRestoreTime" type="date"/>
                <parameter name="displayId" type="int" readOnly="true"/>
              <parameter name="cases.header.ticket" type="header"/>
                <parameter name="operationalImpact"      type="stringList"/>
                <parameter name="securityClassification" type="stringList"/>
                <parameter name="consequenceSeverity"    type="stringList"/>
              <parameter name="cases.header.ticket" type="header"/>
                <parameter name="attackLocationId"       type="string"/>
                <parameter name="common" type="commonResourceAttrs"/>
          </component>
          <component name="actionsTaken" type="textarea" mandatory="true"/>
      </tab>
      <tab name="cases.tab.followup" type="base">
          <component name="plannedActions" type="textarea" />
          <component name="recommendedActions" type="textarea" />
          <component name="followupContact" type="textarea" />
      </tab>
</editor>
<!--
     <editor enforceLocking="true" colorTreeBy="consequenceSeverity" width="480" height="480">
  <tab name="cases.tab.initial" type="container">
      <tab name="cases.tab.attributes" type="base" showExport="true">
          <component name="attributesTable" type="table">
              <parameter name="cases.header.case" type="header"/>
              <parameter name="name"         type="resourceName"/>
              <parameter name="displayId"    type="int" readOnly="true"/>
              <parameter name="cases.header.ticket" type="header"/>
              <parameter name="ticketType"             type="stringList"/>
              <parameter name="stage"                  type="stringList"/>
              <parameter name="frequency"              type="stringList"/>
              <parameter name="operationalImpact"      type="stringList"/>
              <parameter name="securityClassification" type="stringList"/>
              <parameter name="consequenceSeverity"    type="stringList"/>
              <parameter name="reportingLevel"      type="int" readOnly="true"/>
              <parameter name="detectionTime"        type="string" readOnly="true"/>
              <parameter name="estimatedStartTime"   type="string" readOnly="true"/>
              <parameter name="estimatedRestoreTime" type="date"/>
              <parameter name="common" type="commonResourceAttrs"/>
          </component>
      </tab>
      <tab name="cases.tab.description" type="base">
          <component name="affectedServices" type="textarea" maxChars="4000"/>
          <component name="affectedElements" type="textarea" maxChars="4000"/>
          <component name="estimatedImpact"  type="textarea" maxChars="4000"/>
          <component name="affectedSites"    type="textarea" maxChars="4000"/>
      </tab>
      <tab name="cases.tab.securityClassification" type="base">
          <component name="securityClassificationTable" type="table">
              <parameter name="cases.header.securityClassification" type="header"/>
              <parameter name="attackMechanism"     type="stringList"/>
              <parameter name="attackAgent"         type="stringList"/>
              <parameter name="incidentSource1"     type="limitedString" minChars="3" maxChars="5"/>
              <parameter name="incidentSource2"     type="limitedString" minChars="3" maxChars="5"/>
              <parameter name="vulnerability"       type="stringList"/>
              <parameter name="sensitivity"         type="stringList"/>
              <parameter name="associatedImpact"    type="stringList"/>
              <parameter name="action"              type="stringList"/>
              <parameter name="cases.header.securityClassificationCode" type="header"/>
              <parameter name="securityClassificationCode" type="string" readOnly="true"/>
          </component>
      </tab>
  </tab>
  <tab name="cases.tab.followup" type="base">
          <component name="actionsTaken"        type="textarea" />
          <component name="plannedActions"      type="textarea" />
          <component name="recommendedActions"  type="textarea" />
          <component name="followupContact"     type="textarea" />
  </tab>
  <tab name="cases.tab.final" type="container">
      <tab name="cases.tab.attackMechanism" type="base">
          <component name="attackMechanismTable" type="table" weight="5">
              <parameter name="attackMechanism"     type="string" readOnly="true"/>
              <parameter name="attackProtocol"      type="string"/>
              <parameter name="attackOs"            type="string"/>
              <parameter name="attackProgram"       type="string"/>
              <parameter name="attackTime"          type="date"/>
          </component>
          <component name="attackTarget"      type="textarea" />
          <component name="attackService"     type="textarea" />
          <component name="attackImpact"      type="textarea" />
          <component name="finalReportAction" type="textarea" />
      </tab>
      <tab name="cases.tab.attackAgent" type="base" >
          <component name="attackAgentTable"        type="table">
              <parameter name="attackAgent"         type="string" readOnly="true"/>
              <parameter name="attackLocationId"    type="string"/>
          </component>
          <component name="attackNode"      type="textarea" weight="2"/>
          <component name="attackAddress"   type="textarea" weight="2"/>
      </tab>
      <tab name="cases.tab.incidentInformation" type="base">
          <component name="incidentInformationTable" type="table">
              <parameter name="incidentSource1"     type="string" readOnly="true"/>
              <parameter name="incidentSource2"     type="string" readOnly="true"/>
          </component>
          <component name="incidentSourceAddress" type="textarea" weight="7"/>
      </tab>
      <tab name="cases.tab.vulnerability" type="base">
          <component name="vulnerabilityTable" type="table">
              <parameter name="vulnerability"       type="string" readOnly="true"/>
              <parameter name="vulnerabilityType1"  type="stringList"/>
              <parameter name="vulnerabilityType2"  type="stringList"/>
          </component>
          <component name="vulnerabilityEvidence"   type="textarea" nbRows="4"/>
          <component name="vulnerabilitySource"     type="textarea" nbRows="4"/>
          <component name="vulnerabilityData"       type="textarea" nbRows="4"/>
      </tab>
      <tab name="cases.tab.other" type="base">
          <component name="otherTable" type="table" weight="4">
              <parameter name="history"             type="stringList"/>
              <parameter name="noOccurrences"        type="int"/>
              <parameter name="lastOccurrenceTime"   type="date"/>
              <parameter name="resistance"          type="stringList"/>
              <parameter name="consequenceSeverity"  type="string" readOnly="true"/>
              <parameter name="sensitivity"  type="string" readOnly="true"/>
          </component>
          <component name="recordedData"        type="textarea" nbRows="4"/>
          <component name="inspectionResults"   type="textarea" nbRows="4"/>
          <component name="conclusions"         type="textarea" nbRows="4"/>
      </tab>
  </tab>
</editor>
 -->
 ```

Pre-requisites for creating 2GP Packages:
	* Create Dev Hub and Dev edition Orgs.
	* Enable Unlocked and Second-Generation Managed Packaging in  Dev Hub Org.
	* Create and Register Your Namespace in DevHub org and link namespace to Dev Org.
	* Install Salesforce CLI in VSCode.
	* Create SFDX Project and Auth DevHub.

1) Authenticate Devhub ORG.
sfdx force:auth:web:login -d -a <Devhub Org Alias>
or
sfdx force:auth:web:login --setdefaultdevhubusername -d -a  <Devhub Org Alias>

example: sfdx force:auth:web:login --setdefaultdevhubusername -d -a  DevHubOrg

2) Create a scratch org to deploy the package

	sfdx force:org:create --durationdays <ScratchOrg Duration> --setdefaultusername --setalias <ScratchOrg Alias> orgname=<ScratchOrg name> edition=Developer

example: sfdx force:org:create --durationdays 2 --setdefaultusername --setalias DevScratchOrg orgname=DevScratchOrg@test.com edition=Developer

* run the below command to open the scratch org

	sfdx force:org:open -u <ScratchOrg Alias>

example: sfdx force:org:open -u DevScratchOrg

3) Deploy the SFDX Project/Package to the scratch org

	sfdx force:source:push

3) Create Package Version

	sfdx force:package:create --name "<Package Name>" --packagetype Managed --path force-app --targetdevhubusername <Devhub Org Alias>

example: sfdx force:package:create --name "My Package" --packagetype Managed --path force-app --targetdevhubusername DevHubOrg


4) Create Beta version of the package

	Without -c (Code Coverage): CLI does not check the codecoverage but the problem with this is that the package cannot be promoted from Beta to Released package.

		sfdx force:package:version:create -p "<Package Name>" -v <Devhub Org Alias> --installationkeybypass
		
	With -c (Code Coverage):

		sfdx force:package:version:create -p "<Package Name>" -v <Devhub Org Alias> -c  --installationkeybypass
	example:
		sfdx force:package:version:create -p "My Package" -v DevHubOrg -c --installationkeybypass

	*After sucessful Package version creation, System will generate Package Version ID and Package versions are beta until you promote them to a released state.

5) Promote package to a released state

       To make your package available for the Production org, we need to promote it to the released state.

		sfdx force:package:version:promote -p <Package VersiobId> -v <Devhub Org Alias>
	example:
		sfdx force:package:version:promote -p 04t........ -v DevHubOrg


Additional Commands:
To track the Package version creation status:
	sfdx package:version:create:report -i <Execution_ID>

display information about the API limits for your Salesforce org:
	sfdx force:limits:api:display

List all versions of a specific package:
	sfdx force:package:version:list
	or
	sfdx force:package:version:list -p <Package Id>

Delete a package and its versions:
	 sfdx force:package:delete --package <Package Version Id> --targetdevhubusername <Devhub Org Alias>    

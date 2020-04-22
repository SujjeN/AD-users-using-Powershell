# AD-users-using-Powershell
Powershell script to deploy AD users in Bulk using .csv file


firstname,middleInitial,lastname,username,email,streetaddress,city,zipcode,state,country,department,password,telephone,jobtitle,company,ou
Utsab,,Parajuli,uparajuli,utsab.parajuli@ktmserver.com,New Baneshwor,Kathmandu,75202,TX,Nepal,Sales,Nepal12345,214-800-4820,Sales Head,Ktmserver,"OU=Sales,DC=ktmserver,DC=com"
Bikasn,Bikram,Shah,bshah,bikash.shah@ktmserver.com,Jorpati,Kathmandu,75202,TX,United States,Sales,Nepal12345,214-800-4820,Sales Manager,Ktmserver,"OU=Sales,DC=ktmserver,DC=com"
Pawon,,Kc,pkc,pawon.kc@ktmserver.com,Thankot,Kathmandu,44600,BG,Nepal,Sales,Nepal12345,,Sales Manager,Ktmserver,"OU=Sales,DC=ktmserver,DC=com"

# Import active directory module for running AD cmdlets
Import-Module activedirectory
  
#Store the data from ADUsers.csv in the $ADUsers variable
$ADUsers = Import-csv 'C:\Users\Administrator\Desktop\ADusers inbulk\bulk_users1.csv'

#Loop through each row containing user details in the CSV file 
foreach ($User in $ADUsers)
{
	#Read user data from each field in each row and assign the data to a variable as below
		
	$Username 	= $User.username
	$Password 	= $User.password
	$Firstname 	= $User.firstname
	$Lastname 	= $User.lastname
	$OU 		= $User.ou #This field refers to the OU the user account is to be created in
    $email      = $User.email
    $streetaddress = $User.streetaddress
    $city       = $User.city
    $zipcode    = $User.zipcode
    $state      = $User.state
    $country    = $User.country
    $telephone  = $User.telephone
    $jobtitle   = $User.jobtitle
    $company    = $User.company
    $department = $User.department
    $Password = $User.Password


	#Check to see if the user already exists in AD
	if (Get-ADUser -F {SamAccountName -eq $Username})
	{
		 #If user does exist, give a warning
		 Write-Warning "A user account with username $Username already exist in Active Directory."
	}
	else
	{
		#User does not exist then proceed to create the new user account
		
        #Account will be created in the OU provided by the $OU variable read from the CSV file
		New-ADUser `
            -SamAccountName $Username `
            -UserPrincipalName "$Username@ktmserver.com" `
            -Name "$Firstname $Lastname" `
            -GivenName $Firstname `
            -Surname $Lastname `
            -Enabled $True `
            -DisplayName "$Lastname, $Firstname" `
            -Path $OU `
            -City $city `
            -Company $company `
            -State $state `
            -StreetAddress $streetaddress `
            -OfficePhone $telephone `
            -EmailAddress $email `
            -Title $jobtitle `
            -Department $department `
            -AccountPassword (convertto-securestring $Password -AsPlainText -Force) -ChangePasswordAtLogon $True
            
	}
}

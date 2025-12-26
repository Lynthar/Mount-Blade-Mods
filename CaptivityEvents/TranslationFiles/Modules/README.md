# Translation Instructions
Please make sure to create a new folder for the language you are translating and remove all references to English. Remove this README as well.

In all files under language folders, rename English folder for your language (French or something else)
 
Previous
<base xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema" type="string">
    <tags>
        <tag language="English" />
    </tags>
    <strings>
        <string id="CEREQEVENTS0001" text="Your enemies take you as a prisoner." />
        
New
<base xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema" type="string">
    <tags>
        <tag language="Français" />
    </tags>
    <strings>
        <string id="CEREQEVENTS0001" text="Vos ennemis vous font prisonnier." />


## Additional Updates

After the update of 1.8.0, there is another setup in the language files (language_data.xml)

Previous
<LanguageData id="Français">
  <LanguageFile xml_path="FR/ModuleStrings.xml" />
</LanguageData>

New
<LanguageData id="Français">
  <LanguageFile xml_path="FR/ModuleStrings.xml" />
</LanguageData>
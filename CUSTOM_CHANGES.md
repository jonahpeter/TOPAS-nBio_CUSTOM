# This is a custom build of TOPAS-nBio 3.0

## Here is a list of changes from vanilla TOPAS-nBio 3.0

11/3/2026 the correction below implementing properly scaled radii yields a nan for type 3 reactions when the effective radius is below the Onsager radius. These means that contact reactions are not implemented even when TestForContactReactions is True.
        - this only affects H3O+ + OH- so it doesn't really matter, but it should be fixed by the developers

11/2/2026 moved ResolveReactionRateCoefficients();
	CalculateContactProbabilities();
	ResolveRemainerReactionParameters(); after pH/ionic scaling (the sampler reads reaction radii and probabilities, NOT kobs, so these must be updated from kobs before being set)
        Affected File: TsIRTConfiguration.cc

11/2/2026 initialized temperature to 25 C
        Affected File: TsIRTConfiguration.hh

11/2/2026 fixed an integer type issue (changed 3/2 to 1.5 in GetDebyeHuckelA())
        Affected File: TsIRTConfiguration.cc
        Affected Function: GetDebyeHuckelA()

11/2/2026 fixed the bug below.
        Affected File: TsIRTConfiguration.cc
        Affected Function: IonicRate()

8/20/2026 - TEMPERATURE DEPENDENCE OF IONIC STRENGTH SCALING CURRENTLY ONLY WORKS AT 25C.
        - this is because the code uses a hard-coded version of the bronsted-bjerrum reaction rate equation that is only correct at 25C
        - must modify to generalize!

4/12/2025 - Implemented scaling from user values for kdif and kact. did this by accessing the default values and multiplying the new values by kabs_factor = default / refKabs, and similar for kdif. also had to define refKabs and refKdif in reaction 2 
    Noticed that Reaction 10 does not update these values (calculates them but doesnt use them). Not sure why. Didn't modify this.
    THIS FIXED THE ISSUE: TEMPERATURE SCALING NOW COINCIDES WITH ITSELF AT 25C
        Affected File: TsIRTConfiguration.cc
        Affected Function: AdjustReactionAndDiffusionRateForTemperature()

4/12/2025 - Fixed the wrong hard-coded diffusion coefficients in use for the temperature scaling of reactions 4, 5, 7, 8, 9, 10. 
    The dynamics still do not coincide at 25C. I think this is because kdif and kact do not coincide. I will have to fix this.
        Affected File: TsIRTConfiguration.cc
        Affected Function: AdjustReactionAndDiffusionRateForTemperature()

4/12/2025 - Fixed the same bug for diffusion coefficients. 
Changed the input to WaterDiffusionRate to "Diff_before" instead of incorrect hard-coded values
Also divided by reference values for electron, H3O, and OHm
        Affected File: TsIRTConfiguration.cc
        Affected Function: AdjustReactionAndDiffusionRateForTemperature()

4/12/2025 - Fixed the bug described below for reaction rate constants. refKdif was incorrectly attributed to 20C instead of 25C. Changed this in TsIRTUtils.cc. 
        Affected File: TsIRTUtils.cc
        Affected Function: WaterDiffusionRate()


4/12/2025 - There are some discrepancies in temperature scaling. Reactions that calculate Kdif on the way to Kobs do that return the user input values at 25C.
            The reason is that D != Da + Db at 25C
    It also seems that not all the diffusion coefficients return their user input values at 25C.
    This has a significant influence on the final H yields, but not much else. Material balance is still satisfied.


TO DO - add reaction between CN^-1 and H

TO DO - add temperature dependence for HCN and Formate reactions
Lin 2004: J. Phys. Chem. A 2004, 108, 8287-829510.1021/jp048854j
    contains temp. data for OH + HCOO^- in Fig. 6

7/1/24 - Fixed pH and ionic strength scaling for Type 6 reactions that are scavenged by something other than H3O^+1 or OH^-1.
        Affected File: TsIRTConfiguration.cc
        Affected Functions: TsIRTConfiguration::AdjustReactionAndDiffusionRateForTemperature(), TsIRTConfiguration::IonicRate()

7/1/24 - Added temperature dependence for supported background reactions. Non supported reactions retain their default (user input) values.
        Affected File: TsIRTConfiguration.cc
        Affected Function: TsIRTConfiguration::AdjustReactionAndDiffusionRateForTemperature()

6/29/24 - Incorporate temperature dependence into ionic strength scaling from first principles.
        Affected File: TsIRTConfiguration.cc
        Affected Function: TsIRTConfiguration::IonicRate 
        
6/29/24 - Added function for temperature dependence of Debye Huckel A constant.
        Affected File: TsIRTConfiguration.cc, TsIRTConfiguration.hh 
        New Function: TsIRTConfiguration::GetDebyeHuckelA()

6/29/24 - Added function for temperature dependence of dielectric constant.
        Affected File: TsIRTConfiguration.cc, TsIRTConfiguration.hh 
        New Function: TsIRTConfiguration::GetDielectricConstant()
        
6/28/24 - Added print statements for temperature scaling of diffusion coefficients. Declared variable "Diff" before the block of if statements.
        Affected File: TsIRTConfiguration.cc
        Affected Function: TsIRTConfiguration::AdjustReactionAndDiffusionRateForTemperature()
        
6/28/24 - Added print statements for pH and H2SO4 concentration
        Affected File: TsIRTConfiguration.cc
        Affected Function: TsIRTConfiguration::AdjustReactionRateForPH() 

6/28/24 - Added some errors to quit simulation when ModelAcidPropertiesWithConcentration = "Generic" is defined with ModelAcidPropertiesWithConcentration or when not defined with ModelAcidPropertiesWithpH.
        Affected File: TsIRTConfiguration.cc
        Affected Function: TsIRTConfiguration::TsIRTConfiguration()  
        
6/27/24 - Added a print statement to tell you when temperature scaling is activated.
        Affected File: TsIRTConfiguration.cc
        Affected Function: TsIRTConfiguration::AdjustReactionAndDiffusionRateForTemperature()  


6/27/24 - Added functionality to specify u:Ch/TOPASChemistry/IonicStrength as an independent parameter.
        This overrides the internal ionic strength calculation based on pH.
        This is only activated when /ModelAcidPropertiesFromSubstance = "Generic" 
        Added a print statement to tell you when ionic strength is being set independently.
        Affected File: TsIRTConfiguration.cc
        Affected Function: TsIRTConfiguration::AdjustReactionRateForPH() 


6/27/24 - Changed temperature scaling to scale from user input values of the 10 default reactions, rather than hard-coded values.
        Affected File: TsIRTConfiguration.cc
        Affected Function: TsIRTConfiguration::AdjustReactionAndDiffusionRateForTemperature() 


6/27/24 - Removed the log output showing "Re-set reaction kobs..." because it was showing inactive reactions.
    Affected File: TsEmDNAChemistry.cc (commented out lines 259, 260, 782)

# COVID19EvictionSimulations
Simulates the impact of evictions on the spread of SARS-CoV-2

## Background

The COVID-19 epidemic has caused an unprecedented public health and economic crisis in the United States. One of the impacts of record levels of unemployement is the looming __eviction crisis__, in which millions of Americans are at risk of losing their homes due to the inability to pay rent (see [Layser et al](https://papers.ssrn.com/abstract=3613789), Aspen Institute [policy memo](https://www.aspeninstitute.org/blog-posts/the-covid-19-eviction-crisis-an-estimated-30-40-million-people-in-america-are-at-risk/), and stories in [NY Times](https://www.nytimes.com/2020/08/09/opinion/evictions-foreclosures-covid-economy.html), [Wash. Post](https://www.washingtonpost.com/business/2020/08/23/economy-federal-benefits-low-wage-workers/), [NPR Boston](https://www.wbur.org/onpoint/2020/08/17/eviction-crisis-looming), [NPR Indianapolis](https://www.wfyi.org/news/articles/housing-advocates-say-eviction-waves-will-spread-covid-19), [Patch](https://patch.com/us/across-america/40m-americans-could-lose-homes-coronavirus-eviction-crisis)). Many cities and states have enacted temporarily legislation banning evictions during the intial months of the pandemic (see lists [here](https://www.nolo.com/legal-encyclopedia/emergency-bans-on-evictions-and-other-tenant-protections-related-to-coronavirus.html), [here](https://evictionlab.org/covid-eviction-policies/)), but the majority of these are set to expire soon, despite the fact that the epidemic is far from under control. Evictions have many detrimental effects on households, but we are particularly worried about how evictions could accelerate the spread of SARS-CoV-2. Studies show that when people are evicted, they tend to "double-up" with other households, effectively increasing household size and household crowding. Many previous [empirical studies](https://www.medrxiv.org/content/10.1101/2020.07.29.20164590v1) as well as our [previous modeling work](https://www.medrxiv.org/content/10.1101/2020.06.04.20121673v1) show how household spread is a major driver of SARS-CoV-2 spread and a major challenge to social-distancing-based control policies. Larger households lead to both more possiblities for the introduction of the virus into the household as well as more opportunities for spread within households. 

Here we use a mathematical model of COVID-19 spread to predict the potential impact of evictions on the epidemic course, both for individuals experiencing evictions and for the population as a whole. This is all very much a work-in-progress, and we will update results as they come. We are currently preparing pre-print with these findings, but given the time-sensitive nature of expiring eviction moratoriums, we opted to get our results out there sooner rather than later. 

This work was conceived of by [Mike Levy](https://www.dbei.med.upenn.edu/bio/michael-z-levy-phd) (UPenn) and the majority of the modeling work was planned and conducted by Justin Sheen (UPenn). Justin has an earlier version of the code on his [Github](https://github.com/jsheen). [Anjalika Nande](https://github.com/anjalika-nande) and I (Harvard) conducted the modeling shown here, and the simulation code we use was created with the help of [Ben Adlam](https://research.google/people/BenAdlam/). Additional input into the conception of the project was provided by urban policy experts [Andrew Greenlee](https://urban.illinois.edu/people/profiles/andrew-greenlee/) and [Daniel Schneider](https://urban.illinois.edu/people/profiles/daniel-schneider/) (UIUC), [Community Legal Services of Philadelphia](https://clsphila.org/), and research assistance was provided by Maria Flor Tejeda and Julianna Schinnick (UPenn). 

Based on some of our preliminary findings, Mike Levy [filed a brief](https://github.com/mzlevy/Philly_Covid/blob/master/28-6_Levy_Declaration.pdf) on Aug 7 with the US District Court in Philadelphia, in regards to a case that an association of property owners, managers, and investers had filed against the City of Philadelphia to prevent them from continuing to enforce anti-eviction polices. On Aug 28, the court ruled in favor of the city, and this mathematical modeling work was cited as evidence that policies enacted by the city and state to prevent evictions were indeed related to reducing the spread of COVID-19. The judge's opinion statement, which cites the evidence from models, will be linked here shortly.

The work presented here extends and details those modeling results for some hypothetical scenarios that are occuring across US cities. 

## Methods

Roughly, the model we use consists of two parts: a network describing the contacts between individuals over which disease can potentially spread, and an infection model describing the stages of COVID-19 an individual passes through if infected. The model is stochastic and coded in Python, and is informed by many studies of COVID-19. Details of this model are described extensively in our pre-print [Dynamics of COVID-19 under social distancing measures are driven by transmission network structure](https://www.medrxiv.org/content/10.1101/2020.06.04.20121673v1), which has many references motivating the choices we made. Some parameter values were updated for this study. We summarize the main features here.

### Contact network

* We divide contacts into 2 types: household or external. The distribution of household sizes is taken from the US census and we assume an individual has equal rate of contact with everyone in their household
* Individuals are connected to a random subset of other individuals in the population. The number of external contacts of each individual at baseline is chosen from a distribution informed by contact surveys and scaled to give the desired parameters described below. Each individual has an equal rate of contact with each of their external contacts. 
* External contacts can be removed/downweighted based on social distancing measures implemented during the epidemic (such as lockdowns, workplace or school closures, mask wearing, etc)
* The probability of disease transmission per contact may be different for household vs external contacts, and is informed by numbers from epidemiological investigations
* We use a population size of 1 million individuals 

### Infection model

We use an SEIRD model:
* S: susceptible individuals
* E: "exposed" individuals who are infected but not yet infections ('latent period')
* I: infected individuals. Subclassified into those with mild/asympomatic infection (I1), and those hospitalized with severe or critical infection (I2, I3). We only consider transmission from individuals who are not hospitalized (I1)
* R: recovered individuals, assumed to be immune
* D: deceased individuals. 

We assume the following parameters. 
* Average duration of latent period:4 days (we assume this ends on average 1 day before symptom onset)
* Average duration of infectious period: 7 days (1 day presymptomatic transmission + 6 days symptomatic/asymptomatic transmission)
* Average time to death (I1 + I2 + I3): 20 days
* Fraction of individuals requring any hospitalization: 5%
* Fraction of individuals requring critical care: 2%
* Fraction of all infected individuals who will die (infection fatality risk, IFR): 1%
* The durations of all infection stages are gamma distributed

Furthermore, we estimate the per contact per day infection rate (&beta;) for both household and external contacts, as well as the effective number of external contacts, by calibrating the model to give
* An overall basic reproduction number before interventions of R<sub>0</sub> = 3 (corresponding to early epidemic doubling time ~4 days)
* A household secondary attack rate of 0.3
* A relative rate of transmission within households vs outside households of &beta;<sub>HH</sub>/&beta;<sub>EX</sub>=2.3

### Control measures

The timelines for COVID-19 spread and control were inspired by the different scenarios that played out across major US metro areas (see pdf in directory for these trajectories for the 50 largest metro areas) (Figure 2). We found that these could roughly be classified into a few different patterns, and we tried to roughly emulate those. 

We simulate control measures by downweighting all external contacts by a fixed percentage described by the intervention efficacy (basically equivalent to randomly deleting a certain percentage of external contacts). We consider the following intervention scenarios
* A __strong lockdown__ implemented on April 1, when the cumulative prevalence of infection was ~ 1%, the cumulative deaths ~ 3/million, and around 100/million cumulative hospitalizations, which reduced external connections by __90%__
* Alternatively, we allowed a __weak lockdown__ implemented on April 1, when the cumulative prevalence of infection was ~ 0.2%, the cumulative deaths < 1/million, and around 10/million cumulative hospitalizations, which reduced external connections by __80%__
* 2 months after the lockdown (June 1), we allowed for a __relaxation__, which reduced the efficacy of the intervention to either __80%__ ('plateau'), __75%__ ('comeback'), or __70%__ ('second wave')
* In some scenarios we allowed for a __second lockdown__, occuring on Nov 1, which always had __90%__ efficacy ('strong')

### Evictions

Evictions were modeled by choosing another random household from the population for the evicted household to merge with (see Figure 1), creating one larger household. No changes to external connections were made. All evictions for the month took place on the first of the month. The __eviction rate__ parameter we use is the percent of all households experiencing eviction each month. 

To estimate the range of possible eviction rates across US cities, we used data from [Eviction Lab](https://evictionlab.org/). Note that eviction rates are often expressed as rates _per rental household_; we additionally use the % of renter households to adjust this to rate _per all households_. Evictions filings have already increased dramatically due to COVID-19, and to estimate how much larger than baseline eviction rates could be, we looked at the fold-increase in unemployement in the same cities (compared to 2019) and assumed that the increase in eviction rates (without any policies preventing evictions) could be increased by the same amount (Figure 3). Unemployement data was from the [Bureau of Labor Statistics](https://www.bls.gov/) via [Department of Numbers](https://www.deptofnumbers.com/unemployment/).

If evictions took place, they occurred starting Sept 1 and continued monthly for the duration of the simulation. 

We consider the following eviction rates: 0% (comparison case) 0.1%, 0.25%, 0.5%, 1%, 2%/month. Because evictions have been blocked in many regions for the past few months and have created a backlog that would have priority if current moratoriums were removed, we assumed that in the first month of evictions (Sept 1), 4 months worth of evictions occured all at once. 


<img src="/figures/hhnetworks.png" width=50% height=50%>

__Figure 1__: _Diagram of the network model over which SARS-CoV-2 can spread. Individuals are connected to others in their house (solid grey lines) and in other households (external contacts, dotted grey lines). When a household experiences eviction (red outline), we assume the residents of that house merge with another house (blue circle), thus increasing their household contacts._ 


<img src="/figures/metros_daily_smooth.png" width=100% height=100%>

__Figure 2__: _Daily cases and death counts (per million, 7 day rolling average) across the 50 largest US metropolitan statistical areas (MSAs). Data is from the county-level reports from the New York Times [Github](https://github.com/nytimes/covid-19-data) and the MSAs were created by aggregating across counties according to definitions provided to us by [CityObsevatory](https://cityobservatory.org/). Code to do this is in our [COVID19NetworkSimulations](https://github.com/alsnhll/COVID19NetworkSimulations) repository._

<img src="/figures/eviction_cities.png" width=50% height=50%>

__Figure 3__: _Eviction rates across selected US cities/districts. Estimated increase in eviction rates is based on increased unemployment rate. Rates are percent of all households experiencing eviction per month. In a city of ~1 million people and a US-average household size of ~2.5, an eviction rate of 0.1%/month corresponds to about 400 evictions per month, and a rate of 2%/month works out to 8000/month. These cities were chosen to represent diversity in size, geography, and eviction rates._


### Other parameters

* Each simulation was initially seeded with 10 infected individuals, chosen randomly
* We ran 10 iterations of each simulation

## Results

We first considered an epidemic trajectory similar to what has been seen across many major cities hit by early outbreaks (e.g. Baltimore, Boston, Chicago, New Orleans, Sacramento, San Jose, Seattle, Detroit): A large early epidemic peak followed by a strong lockdown that dramatically reduced cases, then a weak relaxation that lead case counts to begin to creep up again (Figure 4). By the end of 2020, with a low eviction rate of 0.25%/month about 1.5% more of the population had caught COVID-19 compared to if there were no evictions. This increase corresponds to ~15,000 excess cases and 150 excess deaths. With a 2%/month eviction rate the infection level was ~13% higher than baseline. Without evictions in this scenario about 16% of the population got infected by the simulation end point (Dec 31). The exact values vary across simulations due to all the stochastic factors in disease spread considered in the simulation. 

We then considered the same scenario but imposed a second lockdown on Nov 1. The epidemic was controlled with or without eviction, but the control was slower and less effective at reducing epidemic size when evictions were allowed to continue (Figure 5). Following the epidemic until March 31 2021 at which point the epidemic was nearly eradicated, the final size was 1.9% bigger with 0.25%/month evictions and 4.4% larger with 2%/month evictions. More evictions leads to more large households, which allow more residual spread to occur under lockdowns. 

<img src="/figures/Figurelock90relax75.png" width=75% height=75%>

__Figure 4__: _Impact of evictions in the context of an epidemic with large first wave, strong lockdown, and slow comeback after relaxation. A) Prevalence of infection over time, measured as individuals in I stages of infection. B) Daily incidence of new infections (new I stages), 7-day running average. Shaded regions represent central 90% of all simulations.The first lockdown had efficacy of reducing external contacts of 90%, and under relaxation they were still reduced by 75%. Simulation start point is day of introduction of 10 first cases and end point is Dec 31 2020. C) Final epidemic size by end of simulation, measured as number of individuals who had ever been in any stage of infection. D) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra % of population infected. E) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra number of people infected. Error bars show interquartile ranges. F) Relative risk of infection for individuals who merged households due to evictions vs individuals who kept their pre-epidemic household._

<img src="/figures/Figurelock90relax75lock90.png" width=75% height=75%>

__Figure 5__: _Impact of evictions in the context of an epidemic with large first wave, strong lockdown, and slow comeback after relaxation, followed by a second lockdown. A) Prevalence of infection over time, measured as individuals in I stages of infection. B) Daily incidence of new infections (new I stages), 7-day running average. Shaded regions represent central 90% of all simulations. Both lockdowns had efficacy of reducing external contacts of 90%, and under relaxation they were still reduced by 75%. Simulation start point is day of introduction of 10 first cases and end point is March 31 2021. C) Final epidemic size by end of simulation, measured as number of individuals who had ever been in any stage of infection. D) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra % of population infected. E) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra number of people infected. Error bars show interquartile ranges. F) Relative risk of infection for individuals who merged households due to evictions vs individuals who kept their pre-epidemic household._

We next looked at a similar scenario of a large initial peak and strong lockdown, but in which the relaxation was more stringent, such that cases stayed at a low plateau (Figure 6). This is similar to what seems to be occuring in for example New York City and DC. At this plateau there were ~<100 new cases per day per million residents. In the context of this very controlled epidemic, evictions still most often lead to increases in disease prevalence, but the effect was more minor. For example with a 0.25% eviction rate on average ~0.5% more people were infected by the end of 2020 but in some simulations, just due to chance events, this was up to 3%. For a higher 2% eviction rate, it was ~ 1% more people infected. Note that these smaller increases still correspond to significant excess cases (~10,000) and deaths across the whole population (~100), and that if the cases start to increase during the eviction period, due for example to school reopenings or more indoor gatherings in colder weather, the predicted increases would be more extreme. 

Note that even for this low eviction rate scenario (0.25%/month), this corresponds to around 1 excess death caused by every 70 households evicted, since by Dec 31st 2020 (4+3) x 0.25% = 1.75% of households or 7000 out of 400,000 total households had been evicted. In the context of a final epidemic size of only around 5% (so with IFR of 1% a total population-level mortality rate of only 1 out of 2,000), this is a relatively large death rate attributable to evictions. 

In addition, note that in all scenarios reported here, even if the population-level epidemic burden was not changed substantially by evictions, the individual risk of infection was always substantially higher for individuals who experienced eviction (or who merged households with those who did), generally falling between a relative risk of 1.1 - 1.5. 

<img src="/figures/Figurelock90relax80.png" width=75% height=575%>

__Figure 6__:  _Impact of evictions in the context of an epidemic with large first wave, strong lockdown, and plateau of cases under relaxation. A) Prevalence of infection over time, measured as individuals in I stages of infection. B) Daily incidence of new infections (new I stages), 7-day running average. Shaded regions represent central 90% of all simulations.The first lockdown had efficacy of reducing external contacts of 90%, and under relaxation they were still reduced by 80%. Simulation start point is day of introduction of 10 first cases and end point is Dec 31 2020. C) Final epidemic size by end of simulation, measured as number of individuals who had ever been in any stage of infection. D) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra % of population infected. E) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra number of people infected. Error bars show interquartile ranges. F) Relative risk of infection for individuals who merged households due to evictions vs individuals who kept their pre-epidemic household._

We next considered dynamics in cities that had smaller initial waves, and so imposed lockdowns when case counts were lower, but implemented weaker lockdowns such that cases reached a plateau, but didn't really decline. Then, when relaxation measures were implemented, they experienced a resurgance of cases. This type pattern played out in many metro areas in the south and southwest, including Atlanta, Austin, Las Vegas, Louisville, Memphis, Miami, Nashville, Dallas, Phonenix, and Houston. In this case, if the epidemic continued to be uncontrolled while evictions occured, the effect could be large. We observed ~2% increase in the population infected under an eviction rate of 0.25%/month and ~12% increase with a 2% eviction rate (Figure 7). Even if a second, stronger lockdown was implemented on Nov 1 (Figure 8). , these increases were similar when measured until the very end of the epidemic (1.7% and 7.4%). 


<img src="/figures/Figurelock80relax75.png" width=75% height=75%>

__Figure 7__: _Impact of evictions in the context of an epidemic with smaller first wave, weak lockdown, and comeback after relaxation. A) Prevalence of infection over time, measured as individuals in I stages of infection. B) Daily incidence of new infections (new I stages), 7-day running average. Shaded regions represent central 90% of all simulations.The first lockdown had efficacy of reducing external contacts of 80%, and under relaxation they were still reduced by 75%. Simulation start point is day of introduction of 10 first cases and end point is Dec 31 2020. C) Final epidemic size by end of simulation, measured as number of individuals who had ever been in any stage of infection. D) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra % of population infected. E) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra number of people infected. Error bars show interquartile ranges. F) Relative risk of infection for individuals who merged households due to evictions vs individuals who kept their pre-epidemic household._

<img src="/figures/Figurelock80relax75lock90.png" width=75% height=75%>

__Figure 8__:  _Impact of evictions in the context of an epidemic with smaller first wave, weak lockdown, and comeback after relaxation, followed by a second lockdown. A) Prevalence of infection over time, measured as individuals in I stages of infection. B) Daily incidence of new infections (new I stages), 7-day running average. Shaded regions represent central 90% of all simulations. The first lockdown had efficacy of reducing external contacts of 80%, and under relaxation they were still reduced by 75%. The second lockdown had 90% efficacy. Simulation start point is day of introduction of 10 first cases and end point is March 31 2021. C) Final epidemic size by end of simulation, measured as number of individuals who had ever been in any stage of infection. D) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra % of population infected. E) Increase in the final epidemic size when comparing the scenario with and without evictions, measured as extra number of people infected. Error bars show interquartile ranges. F) Relative risk of infection for individuals who merged households due to evictions vs individuals who kept their pre-epidemic household._


## Discussion

In summary, our analysis suggests that evictions have a measurable impact on the spread of COVID-19, and suggest that policies to prevent evictions are an important component of epidemic control. Evictions both increase the growth rate of a growing epidemic but also compromise the ability of even strong lockdowns to reduce case levels. Evictions have a larger impact on the final epidemic size when eviction rates are higher, either at baseline or due to unemployment-rate-driven increases, and when the epidemic is growing - even if slowly - during the time evictions are occuring.

Caveats
* We have not attempted to recreate the exact trajectory of COVID-19 cases/deaths in a particular city, instead, we consider a few scenarios that capture the major patterns as many cities across the country. Our code is all freely available and we encourage anyone advising an individual city to adapt it to their scenario. 
* It's difficult to estimate what level of eviction rate any particular city might be facing. This Aspen Institute [policy memo](https://www.aspeninstitute.org/blog-posts/the-covid-19-eviction-crisis-an-estimated-30-40-million-people-in-america-are-at-risk/) gives lots of data and methods for making these estimates. The scenario of _40 million Americans facing evictions_ described in their title is roughly equivalent to our eviction rate of 2%/month over 7 months. 
* Not all households facing eviction double up with other households. Some individuals may experience homelessness and have to stay at shelters. We believe that including shelters could only increase the impact of evictions on cases of COVID-19, since shelters have been documented as sites of large outbreaks of infection (see [CDC MMWR](https://www.cdc.gov/mmwr/volumes/69/wr/mm6917e1.htm?s_cid=mm6917e1_e&deliveryName=USCDC_921-DM26442) and [Nature News](https://www.nature.com/articles/d41586-020-01389-3))
* We model a metro area of ~1 million individuals as relatively randomly mixing, with no correlation between baseline household size, probability of eviction, and degree of reduction in external connections under social distancing policies. In reality, cities are segregated, with evictions being clustered in neighborhoods with lower socioeconomic status, higher likelihoods of job loss, less ability to "work-from-home", and higher baseline rates of crowding in households, and furthermore, individiuals tend to mix more with individuals of similar geographic and demographic groups. Studies have found that these groups are also more likely to be have been infected with COVID-19. The likely result of including this heterogeneity is that the negative effect of evictions on case counts would be concentrated in lower income groups, but we still expect spillover to other groups in the city. 
We are currently running simulations to test this. 
* We have not modeled the "rehousing" that can occur when evicted individuals eventually regain the income needed to house their household separately. Since the economic hardships of the epidemic are unlikley to disappear until the infection is under control, and since we have only simulated a limited timeframe, for simplicity we did not include this effect
* Our specific numeric results depend on the parameter values we have chosen. One input that is important for estimating parameter values is the _household secondary attack rate_, defined as the probability that an infected individual transmits to any particular household member. Different studies have given very different estimates of this quantitiy. Here we used a central estimate, and will conduct sensitivity analysis around this value in the future. There is also uncertainty in the distribution of the effective number of external contacts individuals have, since surveys and mobility trackers have trouble capturing contacts truly relevant to spread of any particular infection. Moreover, it is unclear what the effective transmission rate across each of these contacts is and how different it is from household contacts. While we estimated each of these quantities to parameterize our model, more sensitivity analysis is required. 

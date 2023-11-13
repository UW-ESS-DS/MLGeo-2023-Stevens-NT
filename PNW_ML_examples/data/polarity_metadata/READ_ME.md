# Stevens_fm_query_for_ESS469.csv  
**:auth:** Nathan T. Stevens
**:email:** ntsteven (at) uw.edu
**:org:** Pacific Northwest Seismic Network
**:license:** MIT (2023)
**:purpose:**  
Plain-language explanation of fields provided in the associated *.csv file exported from PNSN `archdb` on server `pp2` on 13. November 2023 by NT Stevens for use by student(s) in the ESS 469 Fall 2023 course offering.

Field Descriptions (for most, see `https://ncedc.org/db/Documents/NewSchemas/PI/v1.6.4/PI.1.6.4/index.htm`)  

***EVENT Table Fields***  
- **evid**: Event ID number from the UW catalog  

***ORIGIN Table Fields***  
- **orid**: Preferred origin ID number  

***ARRIVAL Table Fields***  
- **arid**: Arrival ID number  
- commid: Comment identification  
- **datetime**: arrival time in epoch (seconds since 1970-01-01T00:00:00)  
- **sta**: Station code  
- **net**: Network code  
- auth: Source of the data  
- subsource: Second identifer to specify data source (solution source, generally)  
- channel: Channel name  
- channelsrc: Domain for channel  
- **seedchan**: SEED channel name  
- **location**: Location code  
- **iphase**: Reported phase name
        `P` - compressional wave, used for first motions analysis
        `S` - shear wave
- **qual**: Onset quality, i = impulsive, e = emergent
- clockqual: Timing quality during observation
- clockcorr: Timing correction to add to observed `datetime` if known
- ccset: Indicates if clock correction was applied to arrivals in computing hypocenter solution 
- **fm**: first motion code   
        `c.` = compression, higher quality observation  
        `d.` = dilitation, higher quality observation  
        `+.` = compression, lower quality observation  
        `-.` = dilitation, lower quality observation  
        `..` = Indeterminite
- ema: emergence angle  
- azimuth: Station to event azimuth (backazimuth) as identified from waveform analysis
- slow: apparent slowness
- **deltim**: Arrival time uncertainty
- delslo: uncertainty in slowness
- **quality**: Arrival quality $\in [0, 1]$ with 1 = good, 0 = bad
- **snr**: Signal to Noise Ratio
- rflag: Review flag
- lddate: Load date (when the data entry was loaded/last updated on the database)

***ASSOCARO Table Fields***  
- **seaz**: Event to station azimuth for a given origin solution  
- **delta**: Source to receiver distance in great-circle degrees  
- **wgt**: Data weighting for origin solution  
- **timeres**: Time residual for origin solution  



```python
# Import dependencies
import pandas as pd
from obspy.clients.fdsn import Client
from obspy import Stream, UTCDateTime

# Load CSV as dataframe, using the ARID as the unique index
df_fm = pd.read_csv('Stevens_fm_query_for_ESS469.csv', index_col='arid')

# Initialize waveform client
client = Client('IRIS')

# Define padding for timing around picks in seconds
pads_sec = [75., 75.]

# Get a single line from the DataFrame
i_series = df_fm.iloc[0,:]

if i_series.iphase in ['P','p']:
    # Prepare query for 3-C waveform from internet
    arid = i_series.name
    net = i_series.net
    sta = i_series.sta
    if i_series.location == '  ':
        loc = ''
    else:
        pass
    chan = f'{i_series.seedchan[:2]}?'
    t0 = UTCDateTime(i_series.datetime) - pad_sec[0]
    t1 = UTCDateTime(i_series.datetime) + pad_sec[1]

    # Bundle Get_Waveforms Key-Word ARGuments
    gwkwargs = dict(
                    zip(['network','station','location','channel','starttime','endtime'],
                        [net,sta,loc,chan,t0,t1]
                    )
                )

    # Get waveforms from internet
    stream = client.get_waveforms(**gwkwargs)
    if len(stream) > 0:
        # Define file naming structure
        fname = f'{net}.{sta}.{loc}.{chan}.{arid:d}.mseed'
        print(f'Saving {fname}')
        # Save waveforms as miniSEED
        stream.write(fname, fmt='MSEED')

```
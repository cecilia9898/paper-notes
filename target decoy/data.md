# Dict of mzML 
================================================================================
File: 00_mzml/ea00021_qy_ultra_pro0_96f1.mzML
--------------------------------------------------------------------------------
Spectrum meta
  index                 : 2
  id                    : controllerType=0 controllerNumber=1 scan=3
  ms level              : 2
  retention time (min)  : -
  FAIMS CV              : -40.000
  m/z range             : 149.6277 – 592.2453
  base peak (m/z,int)   : (383.2186, 4.797e+04)
  total ion current     : 1.17e+05
  defaultArrayLength    : 9
  precursor (m/z, z)    : (-, -)
  isolation target m/z  : -
  isolation offsets     : -- / +-
  head(m/z,intensity):
     1. 149.627731      9302.0625
     2. 181.659775      10681.238
     3. 203.289078      2315.3533
     4. 272.172485      18654.479
     5. 309.660156      2091.6863
     6. 383.218567      47966.312
     7. 438.689270      2591.8167
     8. 494.264069      20552.62
     9. 592.245300      2858.4429
  All keys: ['index', 'id', 'defaultArrayLength', 'scanList', 'precursorList', 'MSn spectrum', 'ms level', 'positive scan', 'centroid spectrum', 'base peak m/z', 'base peak intensity', 'total ion current', 'FAIMS compensation voltage', 'lowest observed m/z', 'highest observed m/z', 'scan description', 'count', 'm/z array', 'intensity array']
  precursorList (nested):
{
  "count": 1,
  "precursor": [
    {
      "spectrumRef": "controllerType=0 controllerNumber=1 scan=1",
      "isolationWindow": {
        "isolation window target m/z": 432.206665039063,
        "isolation window lower offset": 0.25,
        "isolation window upper offset": 0.25,
        "ms level": 1
      },
      "selectedIonList": {
        "count": 1,
        "selectedIon": [
          {
            "selected ion m/z": 432.206665039063,
            "charge state": 2,
            "peak intensity": 122648.6274414
          }
        ]
      },
      "activation": {
        "collision-induced dissociation": "",
        "collision energy": 22.0
      }
    }
  ]
}

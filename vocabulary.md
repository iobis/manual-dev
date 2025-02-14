## Choosing Vocabularies for your dataset

### Map your data with DwC vocabulary

There are many possible ways of setting up your datasheets, and if you are new to OBIS you likely did not use controlled Darwin Core (DwC) or BODC vocabulary before samples were collected. In mapping your data fields to DwC we recommend documenting your choices so you have a reference to go back to should the need arise. In such a document you should take notes on the choices you made, as well as any actions you had to take (e.g. separate one column into many, convert dates or coordinates, etc.).

For example, a DwC mapping reference table could look like the following:

| Verbatim field name | Mapped DwC term | Actions taken | Notes |
|-------|-------|------------|--------------|
| date | eventDate | convert dates to ISO |  |
| coordinates| decimalLongitude, decimalLatitude | convert ddmmss to decimal degrees, separated one column into 2 for longitude and latitude | put original coordinates into verbatimCoordinates |

In order to help you map your data to DwC terms, we have provided the table below which outlines some common data fields, their associated Darwin Core vocabulary, and which data table the field is likely to go in:

Common Raw Terms | DwC Field | Data table
----------------- | ----------------------------- | ---------
Date, Time | eventDate | Event, Occurrence
Species, g_s, taxa | scientificName | Occurrence
Any biotic/abiotic measurements* | measurementType, measurementValue, measurementUnit* | eMoF
Depth | maximumDepthInMeters or minimumDepthInMeters | Event, Occurrence
Lat/Latitude, Lon/Long/Longitude, dd | decimalLatitude, decimalLongitude | Event, Occurrence
Sampling method | samplingProtocol | eMoF
Sample size, N, #, No. | sampleSizeValue | eMoF
Location | locality | Event
Presence, absence | occurrenceStatus | Occurrence
Type of record/ specimen | basisofRecord | Occurrence
Person/ people that recorded the original Occurrence | recordedBy | Occurrence
OrcID of person/ people that recorded the original Occurrence | recordedByID | Occurrence
Person/ people that identified the organism | identifiedBy | Occurrence
OrcID of person/ people that identified the organism | identifiedByID | Occurrence
Data collector, data creator | recordedBy | Event, Occurrence
Taxonomist, identifier | identifiedBy | Occurrence
Record number, sample number, observation number | occurrenceID (either ID or incorporated into ID) | Occurrence

*Note that mapping abiotic/biotic measurement fields (sex, temperature, abundance, lengths, etc.) will occur within the [extendedMeasurementOrFact extension](format_emof.html). Here this data will go from being a separate column to being condensed into the `measurementType` and `measurementValue` fields.*

The obistools R package also has the [map_fields function](https://github.com/iobis/obistools#map-column-names-to-darwin-core-terms) that you can use to map your dataset fields to a DwC term.

## How to map eMoF terms to preferred BODC vocabulary

### MeasurementOrFact vocabularies

The MeasurementOrFact terms `measurementType`, `measurementValue`, and `measurementUnit` are completely unconstrained and can be populated with free text. While free text offers the advantage of capturing complex and as yet unclassified information, the inevitable semantic heterogeneity (e.g., of spelling or wording) becomes a major challenge for effective data integration and analysis. For example, if you were interested in finding all records related to length measurements, you would have to try to account for all the different ways “length” was recorded by data providers (length, Length, len, fork length, etc.).

Hence, OBIS added 3 new terms: `measurementTypeID`, `measurementValueID` and `measurementUnitID` to standardize the measurement types, values and units. Note however `measurementValueID` is **not** used for standardizing numeric measurements.

These three new terms should be populated using controlled vocabularies referenced using Unique Resource Identifiers (URIs). OBIS recommends using the internationally recognized [NERC Vocabulary Server](http://www.bodc.ac.uk/resources/products/web_services/vocab/), developed by the British Oceanographic Data Centre (BODC), which can be searched through [https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search). It also compiles vocabulary and terms from different institutions and authorities (e.g., ISO, SeaDataNet), and you can relate your data to them. Such controlled vocabulary is incredibly important to ensure datasets in OBIS will be interoperable - readable by both humans and machines. In this way, you could search for a single `measurementTypeID` and obtain all related records, regardless of differences in wording.

We provide some specific guidance for these URI fields below.

#### measurementTypeID

> Important note!
> **P01 codes are required for the `measurementTypeID` field**.

You can use codes from other collections (e.g. P06, Q01) for `measurementValueID` and `measurementUnitID` fields, but for `measurementTypeID` you must always use a code from the P01 collection (limited exceptions, see below).

##### Selecting P01 codes for measurementTypeID (WORK IN PROGRESS)

P01 codes are concepts that are constructed from several associated elements (see [P01 wheel](https://github.com/nvs-vocabs/P01/blob/master/P01_wheel.pdf)) in order to describe a measurement type. It is important to understand that each element within a P01 code is meant to describe an aspect of the measurement: what is the measurement, what is the object being measured, where was the measurement taken, in what environment, by what kind of methods? By taking together all these elements, we are able to have a unique and specific description to differentiate one measurement from another. To further understand the P01 code and the semantic model it is based on, you can read documentation [here](https://github.com/nvs-vocabs/P01).

The P01 collection is found [here](http://vocab.nerc.ac.uk/collection/P01/current/) and can be [searched through the NERC vocabulary server](https://vocab.nerc.ac.uk/search_nvs/P01/).

You may notice when searching for measurement types related to an occurrence that specific taxonomic codes are available to you, e.g., abundance of Notommata. For OBIS, **all P01 codes should be generalized** - i.e. **do not** select species-specific codes. Instead only choose codes for “biological entities specified elsewhere” when the measurement is related to an occurrence record.

To assist you in selecting the correct P01 code, we are working with [BODC](https://www.bodc.ac.uk/) to develop decision trees. These decision trees will help you understand the uniqueness of your data measurement (e.g. to differentiate abundance per unit area of the bed vs per unit volume of the water body). An individual element of a P01 code may be found within different vocabulary collections (e.g. S02, L22, S22, etc.), but combining concepts together points you to a P01 code that most accurately represents your measurement type.

General guidelines for understanding the different elements that make up a measurementType are in development and will be added to this page soon. In the meantime we recommend using either the the [SeaDataNet P01 vocabulary search](https://vocab.seadatanet.org/p01-facet-search) or the [NERC vocabulary builder](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_builder/) to assist in finding P01 codes.

For measurementTypes related to sampling instruments and/or methods attributes, see the Q01 collection:

* Vocabulary: [http://vocab.nerc.ac.uk/collection/Q01/current/](http://vocab.nerc.ac.uk/collection/Q01/current/)
* Search: [https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/Q01/](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/Q01/)

#### measurementValueID

The `measurementValueID` field provides an identifying code for measurementValues that are non-numerical (e.g., sampling related, sex or life stage designation, etc.). It is not used for standardizing numeric measurements.

See examples:

* Sampling instruments and sensors (SeaVoX Device Catalogue)
  * documentation: [https://github.com/nvs-vocabs/L22](https://github.com/nvs-vocabs/L22)
  * vocabulary: [http://vocab.nerc.ac.uk/collection/L22/current](http://vocab.nerc.ac.uk/collection/L22/current)
  * search: [https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/L22/](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/L22/)
* Sampling instrument categories (SeaDataNet device categories)
  * documentation: [https://github.com/nvs-vocabs/L05](https://github.com/nvs-vocabs/L05)
  * vocabulary: [http://vocab.nerc.ac.uk/collection/L05/current](http://vocab.nerc.ac.uk/collection/L05/current)
  * search: [https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/L05/](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/L05/)
* Vessels (ICES Platform Codes)
  * vocabulary: [http://vocab.nerc.ac.uk/collection/C17/current](http://vocab.nerc.ac.uk/collection/C17/current )
  * search: [https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/C17/](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/C17/)
* Sex (Gender)
  * documentation: [https://github.com/nvs-vocabs/S10](https://github.com/nvs-vocabs/S10)
  * vocabulary: [http://vocab.nerc.ac.uk/collection/S10/current/](http://vocab.nerc.ac.uk/collection/S10/current/)
  * search: [https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/S10/](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/S10/)  
* Lifestage
  * documentation: [https://github.com/nvs-vocabs/S11](https://github.com/nvs-vocabs/S11)
  * vocabulary: [http://vocab.nerc.ac.uk/collection/S11/current/](http://vocab.nerc.ac.uk/collection/S11/current/)
  * search: [https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/S11/](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/S11/)
* Papers or manuals on the sampling protocol used
  * DOI
  * Handle for publications on IOC's [Ocean Best Practices repository](http://www.oceanbestpractices.net/), for example: [http://hdl.handle.net/11329/304](http://hdl.handle.net/11329/304)

#### MeasurementUnitID

The `measurementUnitID` field is used to provide a URI for specific units. You should populate this field with codes from the [P06](https://vocab.nerc.ac.uk/search_nvs/P06/?searchstr=&options=identifier,preflabel,altlabel,status_accepted&rbaddfilter=inc&searchstr2=) collection.

Some relevant links for this field include:

* Documentation: [https://github.com/nvs-vocabs/P06](https://github.com/nvs-vocabs/P06)
* Vocabulary list: [http://vocab.nerc.ac.uk/collection/P06/current](http://vocab.nerc.ac.uk/collection/P06/current)
* Search list: [https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/P06/](https://www.bodc.ac.uk/resources/vocabularies/vocabulary_search/P06/)

### Requesting new vocabulary terms

If you have already tried looking for a P01 code and were unable to identify a suitable code for your `measurementType` you must then request a code to be created. To do this, your request must be submitted via:

* Submit request through the OBIS Vocabulary GitHub repository (<https://github.com/nvs-vocabs/OBISVocabs/issues>)
  * Requests can also be emailed to vocab.services@bodc.ac.uk if you cannot access GitHub
* Registration with the [BODC Vocabulary Builder](http://vocab.nerc.ac.uk/collection/P01/current/ENTSEX01/)
  * Note: these requests should be based on combinations of existing concepts

#### How to Submit a GitHub Vocabulary Request

1. Navigate to <https://github.com/nvs-vocabs/OBISVocabs/issues> and click on the New Issue button.

![*Screenshot of how to request a new vocabulary on Github*](images/Vocab_GithubRequest_1-NewIssue.png){width=50%}

2. Click Get started

![*Screenshot of submitting an issue to Github*](images/Vocab_GithubRequest_2-GetStarted.png){width=80%}

3. Fill in the title with short details of your request or issue. Then fill in the description. It is recommended to list any existing terms that are similar to your request, or concepts that are sub-components of the request.

![*Screenshot for how to request a new measurementType on Github*](images/Vocab_GithubRequest_3-IssueSubmission.png){width=50%}

4. *Example*: An issue was created to address difficulties in identifying P01 codes for sex rather than gender. Gender is a concept generally applied to humans, whereas “sex” is more applicable for animals. Thus the request was to either modify the current gender P01 code, or create a P01 code that specifies sex, not gender. At the time the request was issued, when users searched for a P01 term for “sex”, only species-specific terms were available.

![*Example of previously requested new term on Github*](images/Vocab_GithubRequest_4-example.png){width=80%}

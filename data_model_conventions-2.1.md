ORA DATA MODEL CONVENTIONS
==========================

## Field naming conventions

### Field levels

ORA fields can be defined as first-level (properties of a record), second level (properties of a record property),
or third-level (properties of a second level property). In JSON:

```
{ "first-level-property":"value",
  "another-first-level-property": 
    [{  "second-level-property": "value"
        "another-second-level-property": 
          [{  "third-level-property": "value" }]
    }]
}
```

An example from the ORA Data Model

```
{ 
  "title":"Title of the work",
  "contributors": 
    [{  "display_name": "First Contributor"
        "roles": 
          [{  "role_title": "Author",
              "role_order": 1 
          }]
      },
      { "display_name": "Second Contributor"
        "roles": 
          [{  "role_title": "Author",
              "role_order": 2 },
            { "role_title": "Depositor",
              "role_order": 1
    }]
  }]
}
```

## Field names in extended format

ORA field names can be given in extended format.

Extended format allows for a field name to indicate it's parent and grandparent fields.

Where a field is a second-level property, this is indicated by the parent first-level field,
a double underscore, and then the field name. For example, a contributor's last name
is indicated by the string `contributors__last_name`.

Third-level properties (i.e. properties of second level properties) have both
their ancestors separated by double underscores: `contributors__roles__role_title`.

The object properties themselves are specified in the data model spreadsheet.


## Field value conventions

Contents:
- Where a value is not set
- Boolean or trinary values
- Date values
- Date/time values
- Date-like fields
- Strings
- Tokenised field values
- URIs
- URLs

### Where a value is not set

The handling for an unset value is dependent on the environment.

- In code, it should be expressed as the value of the language primitive for null values: e.g. `None` (python), `Nil` (Ruby), `null` (Javascript)
- In XML serialisation it should not be expressed at all - i.e. in XML the value will not be present, rather than <mods:identifier type="isbn10"/>
- In Solr, the value should not be set
- In JSON serialisation, the `null` primitive should be used

### Boolean or trinary values

- If a data format is boolean OR trinary, then a string shall be used with the value "yes", "no" or the equivalent for an unset value in that environment
- The data model spreadsheet shall indicate that a field is boolean/trinary
- The data model spreadsheet shall indicate the default value

### Date values
- Dates shall be stored and serialized as ISO strings, in the format YYYY-MM-DD
- The data model spreadsheet shall indicate that a field is a date value, the level of required precision (e.g. year, day) and any default values
- Specific implementations, e.g. Solr, may choose to add additional precision where this is necessary for storage
- If precision needs to be added to a date field, the earliest legitimate value will be used e.g 1978-01-01 for day level precision for the year 1978

### Date/time values ("datestamps")
- Datestamps shall be stroed and serialized as ISO formated UTF8 strings, "YYYY-MM-DDTHH:MM:SSZ"
- The data model spreadsheet shall indicate when a field is a date/time, the level of required precision (e.g hour, minute), and any default values
- Specific implementations, e.g. Solr, may choose to add additional precision where this is necessary for storage
- Datestamps shall be timezoned to GMT, values shall be converted where necessary to their GMT equivalents
- If precision needs to be added to date/time field, the earliest legitimate value will be used e.g 1978-05-05T00:00:00Z for time level precision for the day May 5, 1978

### Date-like fields 
- Fields containing date-like information that are not ISO dates shall be stored and serialized as strings for later processing (e.g `data_coverage_temporal_start_date`)
- The data model spreadsheet shall indicate that a field is a date field that is stored as a free text string

### Strings
- String values shall be stored and serialized in UTF8
- String format is the default value in the data model spreadsheet, and does not need to be specified

### Tokenised string values
A tokenised string value is a string that comes from a limited list of legitimate values, e.g "English", "Open access", "38b"

- The string values stored and serialized for a field will be specified the data model spreadsheet or linked to from it.
-- This value can be converted for solrization or intermediate format purposes!
- The spreadsheet shall indicate where a value is a tokenised value, and the location of the list of valid values and their display equivalents where relevant
-- In linked YML files, the `id` shall be the value, the `term` is what is displayed to the user at a given time
- These tokenised values will become part of the datamodel

Specific rules
- `language` shall be stored and serialized as a string, which equate to the language name value in ISO 639-2 or 639-3, e.g. "English", "French, Old (842–ca. 1400)"
- `subjects` shall be FAST subject headings stored and serialized as a string
-- Validation of FAST subjects is a post migration task
- Affiliation values (`contributors__department`, `contributors__division`, `contributors__research_group`, `contributors__sub_division`, `contributors__sub_unit`, `contributors__oxford_college`) shall be derived from BizTalk
-- Affiliation values shall use the BizTalk UPPER CASE format, e.g. "CONTINUING EDUCATION"
-- College affiliations (`contributors__oxford_college`) shall use the BizTalk short form, e.g. "QUEENS" not "THE QUEEN'S COLLEGE"

### URIs
- URIs shall be stored and serialized as strings
- URIs shall be valid URIs
- The data model spreadsheet shall indicate where a value is a URI and not a URL

### URLs
- URLs shall be stored and serialized as strings
- URLs must be valid URLs and include protocol
- No trailing slashes
- The data model spreadsheet shall indicate when a value is a URL
- URLS shall use https over http, and web addressable formats over others
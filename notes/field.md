# Field

Field

        Field (abstract)
        |
        +--Field_bit
        |  +--Field_bit_as_char
        |  
        +--Field_num (abstract)
        |  |  +--Field_real (asbstract)
        |  |     +--Field_decimal
        |  |     +--Field_float
        |  |     +--Field_double
        |  |
        |  +--Field_new_decimal
        |  +--Field_short
        |  +--Field_medium
        |  +--Field_long
        |  +--Field_longlong
        |  +--Field_tiny
        |     +--Field_year
        |
        +--Field_str (abstract)
        |  +--Field_longstr
        |  |  +--Field_string
        |  |  +--Field_varstring
        |  |  +--Field_blob
        |  |     +--Field_geom
        |  |
        |  +--Field_null
        |  +--Field_enum
        |     +--Field_set
        |
        +--Field_temporal (abstract)
           +--Field_time_common (abstract)
           |  +--Field_time
           |  +--Field_timef
           |
           +--Field_temporal_with_date (abstract)
              +--Field_newdate
              +--Field_temporal_with_date_and_time (abstract)
                 +--Field_timestamp
                 +--Field_datetime
                 +--Field_temporal_with_date_and_timef (abstract)
                    +--Field_timestampf
                    +--Field_datetimef
    

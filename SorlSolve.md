# SOLR excises solutions:
1. Fields - exercise
   1. Add fields to Client Schema
        ```json 
        {
            "add-field": {
                "name": "birth_date",
                "type": "pdate",
                "stored": true,
                "indexed": true,
                "docValues": true
            },
            "add-field": {
                "name": "name",
                "type": "string",
                "stored": true,
                "indexed": true,
                "docValues": true
            },
            "add-field": {
                "name": "last_name",
                "type": "string",
                "stored": true,
                "indexed": true,
                "docValues": true
            },
            "add-field": {
                "name": "description",
                "type": "text_en",
                "stored": true,
                "indexed": true
            },
            "add-field": {
                "name": "age",
                "type": "pint",
                "stored": true,
                "indexed": true,
                "multiValued": true
            },
            "add-field": {
                "name": "salary",
                "type": "pdouble",
                "stored": true,
                "indexed": true
            },
            "add-field": {
                "name": "cars",
                "type": "string",
                "stored": true,
                "indexed": true,
                "multiValued": true
            },
            "add-field": {
                "name": "personal_data",
                "type": "string",
                "stored": true,
                "indexed": true,
                "multiValued": true
            },
            "add-dynamic-field": {
                "name": "*_additionalInfo",
                "type": "text_en",
                "stored": true,
                "indexed": false
            },
            "add-copy-field": {
                "source": "name",
                "dest": "personal_data"
            },
            "add-copy-field": {
                "source": "last_name",
                "dest": "personal_data"
            },
            "add-copy-field": {
                "source": "age",
                "dest": "personal_data"
            },
            "add-copy-field": {
                "source": "salary",
                "dest": "personal_data"
            }
        }
        ```
2. Nested documents - exercise
   1. Add nested field to Clients Schema (...)
        ```json
        {
            "add-field": {
                "name": "note",
                "type": "string",
                "stored": true,
                "indexed": true
            }, "add-field": {
                "name": "address_line_1",
                "type": "string",
                "stored": true,
                "indexed": true
            },
            "add-field": {
                "name": "address_line_2",
                "type": "string",
                "stored": true,
                "indexed": true
            },
            "add-field": {
                "name": "addresses",
                "type": "string",
                "stored": true,
                "indexed": true
            }
        }
        ```
   2. Add document with child
        ```json
        {
            {
                "id": "1_1",
                "name": "Bill",
                "last_name": "Gates",
                "birth_date": "1999-11-03T07:00:00Z",
                "description": "nice cilent",
                "age": 80,
                "salary": 1000000.445,
                "bill_additionalInfo": "smart",
                "cars": ["toyota", "opel"],
                "addresses": [{
                        "id": "1_1_1",
                        "address_line_1": "Hilton Square",
                        "address_line_2": "234 New York",
                        "note": "home"
                    },
                    {
                        "id": "1_1_2",
                        "address_line_1": "Trump Square",
                        "address_line_2": "44 New Jersey",
                        "note": "office"
                    }
                ]
            }, {
                "id": "1_2",
                "name": "Jeff",
                "last_name": "Bezos",
                "birth_date": "1977-11-07T07:00:00Z",
                "description": "bad client",
                "age": 66,
                "salary": 10000040.445,
                "jonh_additionalInfo": "I do not know",
                "cars": ["skoda", "tesla"],
                "addresses": [{
                        "id": "1_2_1",
                        "address_line_1": "Baca Square",
                        "address_line_2": "234 California",
                        "note": "home"
                    },
                    {
                        "id": "1_2_2",
                        "address_line_1": "UJ Square",
                        "address_line_2": "44 New Int",
                        "note": "office"
                    }
                ]
            }
        }
        ```
   3. Add document without child then add it using atomic upadate
      1. add doc without child
            ```json
                {
                        "id": "1_3",
                        "name": "Jacek",
                        "last_name": "Kołodziej",
                        "birth_date": "1954-11-07T07:00:00Z",
                        "description": "fast client",
                        "age": 88,
                        "salary": 548,
                        "jack_additionalInfo": "lorem ipsum",
                        "cars": ["fiat", "porshe"]
                }
            ```
      2. add child using atomic update
            ```json
            {
                "id": "1_3",
                "addresses": {
                    add: {
                        "id": "1_3_1",
                        "address_line_1": "Szybka 62",
                        "address_line_2": "00-000 Warszawa",
                        "note": "home"
                    }
                }
            }
            ```
   4. Modify latest added document replace note
        ```json
        {
            "id": "1_3_1",
            "_root_": "1_3",
            "note": { "set": "zmieniono" }
        }
        ```
   5. delete latest child
        ```json
        {
            "id": "1_3",
            "_root_": "1_3",
            "addresses": { "remove": { "id": "1_3_1" } }
        }
        ```
3. Search - exercises
   1. . Query elements which are created by any John in two ways. (bonus try weigths)
      1. first method, put in q ``created_by: John*``
      2. second method:
         1. select edismax as search engine
         2. put ``John *`` in q
         3. put ``created_by`` in qf
         4. if you want test weights put in qf ``created_by^5`` where 5 is a weight
   2.  Query elements which not are created by any John in two ways
       1.  same as 1. but insteat of ``John*`` put `` -John``
   3. Query for elements which are created by John and has nice stuff in order_description
      1. q: ``(order_description: "nice stuff") ^ 4 AND(created_by: Jonh * ) ^ 2``
   4. Query for element which is never than 7 years
      1. q: ``creation_date: [NOW - 7 YEAR / YEAR TO NOW]``
4. Search - nested documents:
   1. Query for all docs with proper child display
      1. q: ``{!child of="*:* -_nest_path_:*" }``
      2. fl: ``*.[child]``
   2. Query only children for parent 1_1
      1. q: ``{!child of="*:* -_nest_path_:*" }id:1_1``
      2. fl: ``*.[child]``
   3. Query for all matching parents where in children has phrase 234 in any address field, return all children for parents
      1. q: ``{!parent which = "*:* -_nest_path_:*" }address_line_2:234* or address_line_1:234*``
   4. Query for all matching parents where in children has phrase 234 in any address field, return only matching children
      1. q: ``{!parent which = "*:* -_nest_path_:*" }address_line_2:234* or address_line_1:234*``
      2. fl: ``*, [child childFilter = "address_line_2:234* OR address_line_2:234* "]``
   5. Query for all matching parents where name is Bill and in children has phrase 234 in any address field, return all children for parents
      1. ``({!edismax qf = 'address_line_1 address_line_2 note'
    v = +234 }) | ({!parent which = ': -_nest_path_:*'
    score = total } {!edismax qf = 'name'
    v = +Bill })``
5. Facets, highlights
   1. Find all orderes where order_details has Szczecin and highlight where resultwas found
      1. q: ``order_details: * Szczecin *``
      2. enable hl
      3. ``hl-fl order_details``
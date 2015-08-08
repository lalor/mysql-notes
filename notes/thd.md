# def

THD is important

    //sql_class.h
    class THD : public MDL_context_owner,
                public Statement,
                pubilic Open_tables_state
    {

    };


Statement:


    //sql_class.h
    class Statement: public Query_arena
    {
    }

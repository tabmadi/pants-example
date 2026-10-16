python_requirement(
    name="django",
    requirements=["django==5.2.7"],
)


python_sources(
    name="lib",
    dependencies=[
        ":django",
        "//srvs/jango/jango",
    ],
)

pex_binary(
    name="manage",
    entry_point="manage.py",
    restartable=True,
)
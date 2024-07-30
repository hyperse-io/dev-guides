# ECS clean disk device space files

cd /opt-bash: cannot create temp file for here-document: No space left on device

```shell
du -sh /opt/applications/*
```

```text
8.0K    /opt/applications/a
5.8G    /opt/applications/b
23G     /opt/applications/c
24K     /opt/applications/d
28K     /opt/applications/e
6.7G    /opt/applications/f
```

```shell
 du -sh /opt/applications/production/*
```

```text
12G     /opt/applications/production/a
8.8G    /opt/applications/production/b
1.1G    /opt/applications/production/c
873M    /opt/applications/production/d
479M    /opt/applications/production/e
```

Step by step, confirm that a specific directory can be temporarily deleted, and conduct file content governance at the same time

# App下build.gradle配置

    sourceSets {
        main {
            res.srcDirs =
                [
                    'src/main/res/layouts/edit',
                    'src/main/res/layouts/material',
                    'src/main/res/layouts/set',
                    'src/main/res/layouts/show',
                    'src/main/res/layouts/welcome',
                    'src/main/res/layouts',
                    'src/main/res'
                ]
        }
    }
    
# res下新建layouts文件,具体如下：

    layouts
        -- edit
            -- layout
            -- drawable
            -- drawable-hdpi
            -- drawable-mdpi
            -- drawable-xhdpi
        -- material
            -- layout
            -- drawable
            -- drawable-hdpi
            -- drawable-mdpi
            -- drawable-xhdpi
        -- set
            -- layout
            -- drawable
            -- drawable-hdpi
            -- drawable-mdpi
            -- drawable-xhdpi
        ...

**资源分包在打包时会整合到同一个文件夹下面，不会对App性能带来影响，却能给我们项目带来方便**

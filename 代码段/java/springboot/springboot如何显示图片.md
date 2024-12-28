# springboot如何显示图片

要通过springboot展示图片，需要配置WebMvcConfigurer

```java
@Configuration
public class InterceptorsConfig implements WebMvcConfigurer {
    private final Logger logger = LoggerFactory.getLogger(this.getClass());
    
    @Resource
    ProjectConfig projectConfig;
    
	/**
     * 通过
     * http://localhost:8088/image/image-repository/888/1/%E5%9B%BE%E7%89%87.jpg
     *
     * 注意这里的端口是 undertow 的端口
     * 访问图片
     *
     * @param registry
     */
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        //其中image表示访问的前缀。serverRealPath是文件真实的存储路径
        //serverRealPath: /Volumes/HD-FOR-MAC/DEV_ENV/upload_space/bone
        String serverRealPath = projectConfig.getUploadPath();
        if(!serverRealPath.endsWith(File.separator)) {
            serverRealPath = serverRealPath + File.separator;
        }

        //ImageResourceConfig.PATH_IMAGE: /image
        //当我们访问项目的 image 路径时，就会映射到本地E盘的 serverRealPath 目录下
        String imageUrlPattern = ImageResourceConfig.PATH_IMAGE + "/**";
        String imageLocation = "file://" + serverRealPath;
        registry.addResourceHandler(imageUrlPattern)
            .addResourceLocations(imageLocation);
        LoggerUtil.debug(logger, 
                         "imageUrlPattern: {} -> imageLocation: {}", 
                         imageUrlPattern, imageLocation);
    }
}
```



projectConfig

```java
@Component
public class ProjectConfig {
    public static final String AVATAR = "avatar";

    /** 用于下载的临时文件路径 */

    private static String fileSystemBasePath;

    public static String getFileSystemBasePath() {
        return fileSystemBasePath;
    }

    @Value("${bone.fileSystemBasePath}")
    public void setFileSystemBasePath(String fileSystemBasePath) {
        ProjectConfig.fileSystemBasePath = fileSystemBasePath;
    }

    /**
     * 获取下载路径
     */
    public String getDownloadPath() {
        if(StrUtil.isBlank(fileSystemBasePath)) {
            return null;
        }
        return Paths.get(fileSystemBasePath, "download").toString();
    }

    /**
     * 获取上传路径
     */
    public String getUploadPath() {
        if(StrUtil.isBlank(fileSystemBasePath)) {
            return null;
        }
        return Paths.get(fileSystemBasePath, "upload").toString();
    }
}
```

对应yml配置文件需要配置

```yaml
# 项目相关配置
bone:
  # 该项目上传下载所使用的根目录
  fileSystemBasePath: /Volumes/HD-FOR-MAC/DEV_ENV/upload_space/bone
```

当访问http://localhost:8088/image/** 时，就去/Volumes/HD-FOR-MAC/DEV_ENV/upload_space/bone 找图片


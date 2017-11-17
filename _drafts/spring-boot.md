#Spring Boot

### Features
- 创建独立的Spring应用，嵌入式容器
- 起步依赖简化依赖配置
- 自动配置
- Actuator

##### Auto-configuration
spring boot通过条件化配置，实现自动配置。
Condition：在bean定义之前检查条件决定是否注册
```
@Configuration
@ConditionalOnClass({ DataSource.class, JdbcTemplate.class })
@ConditionalOnSingleCandidate(DataSource.class)
@AutoConfigureAfter(DataSourceAutoConfiguration.class)
public class JdbcTemplateAutoConfiguration {

	private final DataSource dataSource;
``
	public JdbcTemplateAutoConfiguration(DataSource dataSource) {
		this.dataSource = dataSource;
	}

	@Bean
	@Primary
	@ConditionalOnMissingBean(JdbcOperations.class)
	public JdbcTemplate jdbcTemplate() {
		return new JdbcTemplate(this.dataSource);
	}

	@Bean
	@Primary
	@ConditionalOnMissingBean(NamedParameterJdbcOperations.class)
	public NamedParameterJdbcTemplate namedParameterJdbcTemplate() {
		return new NamedParameterJdbcTemplate(this.dataSource);
	}

}
```

##### Profile
根据环境激活配置
- 命令行 -Dspring.profiles.active=prd
- 配置文件 spring.profiles.active
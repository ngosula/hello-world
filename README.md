# hello-world
hello world
How are you
# Rest Controller

	# Get

	 @GetMapping(value = "/customers")
	    public List<Customer> getAllCustomers() {
		return customerRepository.findAll();
	    }
    
	 @GetMapping(value = "/customers/{id}")
	    public ResponseEntity<Customer> getCustomer(@PathVariable Long id) {
		Optional<Customer> customer = customerRepository.findById(id);
		if (customer.isPresent())
		    return ResponseEntity.ok().body(customer.get());
		else return ResponseEntity.notFound().build();
	    }

		# Post

	 @PostMapping(value = "/customers")
	    public Customer saveCustomer(@RequestBody Customer customer) {
		return customerRepository.save(customer);
	    }

		# Put

	 @PutMapping(value = "/customers/{id}")
	    public ResponseEntity<Customer> updateCustomer(@PathVariable Long id, @RequestBody Customer customer) {
		Optional<Customer> customer1 = customerRepository.findById(id);
		if (customer1.isPresent()) {
		    return ResponseEntity.ok().body(customerRepository.save(customer));
		}
		return ResponseEntity.notFound().build();
	    }

		# Delete

	 @DeleteMapping(value = "/customers/{id}")
	    public ResponseEntity deleteCustomer(@PathVariable Long id) {
		Optional<Customer> customer = customerRepository.findById(id);
		if (customer.isPresent()) {
		    customerRepository.deleteById(id);
		    return ResponseEntity.ok().build();
		} else
		    return ResponseEntity.notFound().build();

	    }
 # Command Runner
 
	 @Bean
	    public CommandLineRunner demo(CustomerRepository repository) {
		return args -> {
		    // save a few customers
		    repository.save(new Customer("FN1", "LN1"));
		    repository.save(new Customer("FN2", "LN2"));
		    repository.save(new Customer("FN3", "LN3"));
		    repository.save(new Customer("FN4", "LN4"));
		    // fetch all customers
		    log.info("Customers found with findAll():");
		    log.info("-------------------------------");
		    for (Customer customer : repository.findAll()) {
			log.info(customer.toString());
		    }
		    log.info("");

		};
	    }
 
# Alternative to CommandLineRunner 

	@Bean
    public ApplicationRunner setup2(CustomerRepository repository) {
    
        return args -> {
            boolean debug = args.containsOption("debug");
            List<String> files = args.getNonOptionArgs();
            System.out.println(debug);
            System.out.println(files);

            // save a few customers

          /*  repository.save(new Customer("FN1", "LN1"));
            repository.save(new Customer("FN2", "LN2"));
            repository.save(new Customer("FN3", "LN3"));
            repository.save(new Customer("FN4", "LN4"));
            // fetch all customers
            log.info("Customers found with findAll():");
            log.info("-------------------------------");
            for (Customer customer : repository.findAll()) {
                log.info(customer.toString());
            }
           */
            log.info("");

        };
    }
 
 # JPA
 
    	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-jpa</artifactId>
	</dependency>
	<dependency>
		<groupId>com.h2database</groupId>
		<artifactId>h2</artifactId>
		<scope>runtime</scope>
		<version>1.4.193</version>
	</dependency>
    
 # Entity


	@Entity
	public class Customer {
	    @Id
	    @GeneratedValue(strategy = GenerationType.AUTO)
	    private Long id;
	    private String firstName;
	    private String lastName;
	    public Customer() {
	    }
	    public Customer(String firstName, String lastName) {
		this.firstName = firstName;
		this.lastName = lastName;
	    }
	    public Long getId() {
		return id;
	    }
	    public void setId(Long id) {
		this.id = id;
	    }
	    public String getFirstName() {
		return firstName;
	    }
	    public void setFirstName(String firstName) {
		this.firstName = firstName;
	    }
	    public String getLastName() {
		return lastName;
	    }
	    public void setLastName(String lastName) {
		this.lastName = lastName;
	    }
	    @Override
	    public String toString() {
		return "Customer{" +
			"id=" + id +
			", firstName='" + firstName + '\'' +
			", lastName='" + lastName + '\'' +
			'}';
	    }
	}

# JPA Repository

	public interface CustomerRepository extends CrudRepository<Customer,Long> {
	    List<Customer> findAll();
	}
  
# OneToMany
	--
	 @OneToMany(targetEntity = Address.class, cascade = CascadeType.ALL, fetch = FetchType.EAGER)
	 List<Address> addresses;
	 ---
	 public interface CustomerRepository extends CrudRepository<Customer,Long> {
	    List<Customer> findAll();
	    List<Customer> findByAddress_zipcode(String zipcode);
	    List<Customer> findByAddress_ZipCode_AndAddress_Street(String zipCode, String street);
	    // Or raw method
	   /* @Query( value = "select * from customer c" +
		    "join customer_addresses ca " +
		    "   on c.id = ca.customer_id " +
		    "join address a " +
		    "   on a.id = ca.addresses_id " +
		    "where c.zip = :zip", nativeQuery = true)
	    Iterable<Customer> getPeopleWithZip(@Param("zip") String zip);
	    */
	}
	
# Lombok

	<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
	</dependency>

	@AllArgsConstructor
	@NoArgsConstructor
	@Getter
	@Setter
	@ToString
	@EqualsAndHashCode
	public class User implements Serializable {
	    int id;
	    private String firstName;
	    private String lastName;
	    private String city;

	}
 
# swagger 

	<dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.8.0</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.8.0</version>
            <scope>compile</scope>
        </dependency>


	@Configuration
	@EnableSwagger2
	public class SwaggerConfiguration  {

	    @Bean
	    public Docket api() {
		return new Docket(DocumentationType.SWAGGER_2)
			.select()
			.apis(RequestHandlerSelectors.any())
			.paths(PathSelectors.any())
			.build()
			.apiInfo(metaData());
			// by default it is available at http://localhost:8080/swagger-ui.html
	    }
	    private ApiInfo metaData() {
		return new ApiInfoBuilder()
			.title("Spring Boot REST API")
			.description("\"Spring Boot REST API for Online Store\"")
			.version("1.0.0")
			.license("Apache License Version 2.0")
			.licenseUrl("https://www.apache.org/licenses/LICENSE-2.0\"")
			.contact(new Contact("Nageswar", "https://nageswarag.io/", "user1@gmail.com"))
			.build();
	    }
	}

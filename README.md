Filter using CriteriaBuilder -

// Dao class

@Repository
public class UserDAO implements IUserDAO {
 
    @PersistenceContext
    private EntityManager entityManager;
 
    @Override
    public List<User> searchUser(List<SearchCriteria> params) {
        CriteriaBuilder builder = entityManager.getCriteriaBuilder();
        CriteriaQuery<User> query = builder.createQuery(User.class);
        Root r = query.from(User.class);
 
        Predicate predicate = builder.conjunction();
 
        for (SearchCriteria param : params) {
            if (param.getOperation().equalsIgnoreCase(">")) {
                predicate = builder.and(predicate, 
                  builder.greaterThanOrEqualTo(r.get(param.getKey()), 
                  param.getValue().toString()));
            } else if (param.getOperation().equalsIgnoreCase("<")) {
                predicate = builder.and(predicate, 
                  builder.lessThanOrEqualTo(r.get(param.getKey()), 
                  param.getValue().toString()));
            } else if (param.getOperation().equalsIgnoreCase(":")) {
                if (r.get(param.getKey()).getJavaType() == String.class) {
                    predicate = builder.and(predicate, 
                      builder.like(r.get(param.getKey()), 
                      "%" + param.getValue() + "%"));
                } else {
                    predicate = builder.and(predicate, 
                      builder.equal(r.get(param.getKey()), param.getValue()));
                }
            }
        }
        query.where(predicate);
 
        List<User> result = entityManager.createQuery(query).getResultList();
        return result;
    }
 
    @Override
    public void save(User entity) {
        entityManager.persist(entity);
    }
}

// Controller class

@Controller
public class UserController {
 
    @Autowired
    private IUserDao api;
 
    @RequestMapping(method = RequestMethod.GET, value = "/users")
    @ResponseBody
    public List<User> findAll(@RequestParam(value = "search", required = false) String search) {
        List<SearchCriteria> params = new ArrayList<SearchCriteria>();
        if (search != null) {
            Pattern pattern = Pattern.compile("(\w+?)(:|<|>)(\w+?),");
            Matcher matcher = pattern.matcher(search + ",");
            while (matcher.find()) {
                params.add(new SearchCriteria(matcher.group(1), 
                  matcher.group(2), matcher.group(3)));
            }
        }
        return api.searchUser(params);
    }
}

// Smaple url 

http://localhost:8080/users?search=lastName:doe,age>25

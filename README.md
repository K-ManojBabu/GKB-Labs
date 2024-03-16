// User.java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    private int age;
    private LocalDate dob;

    // Getters and setters
}

// UserRepository.java
@Repository
public interface UserRepository extends CrudRepository<User, Long> {
}

// UserController.java
@Controller
public class UserController {
    @Autowired
    private UserRepository userRepository;

    @GetMapping("/")
    public String showForm(Model model) {
        model.addAttribute("user", new User());
        return "userForm";
    }

    @PostMapping("/submit")
    public String submitForm(@ModelAttribute("user") @Valid User user, BindingResult bindingResult) {
        if (bindingResult.hasErrors()) {
            return "userForm";
        }
        userRepository.save(user);
        return "redirect:/users";
    }

    @GetMapping("/users")
    public String getUsers(Model model) {
        Iterable<User> users = userRepository.findAll();
        model.addAttribute("users", users);
        return "userList";
    }
}

// userForm.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>User Input Form</title>
</head>
<body>
    <h1>User Input Form</h1>
    <form action="/submit" method="post" th:object="${user}">
        <label for="name">Name:</label><br>
        <input type="text" id="name" name="name" th:field="*{name}" required><br>
        <label for="email">Email:</label><br>
        <input type="email" id="email" name="email" th:field="*{email}" required><br>
        <label for="age">Age:</label><br>
        <input type="number" id="age" name="age" th:field="*{age}" required><br>
        <label for="dob">Date of Birth:</label><br>
        <input type="date" id="dob" name="dob" th:field="*{dob}" required><br><br>
        <input type="submit" value="Submit">
    </form>
</body>
</html>

// userList.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>User List</title>
</head>
<body>
    <h1>User List</h1>
    <table border="1">
        <thead>
            <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Email</th>
                <th>Age</th>
                <th>Date of Birth</th>
            </tr>
        </thead>
        <tbody>
            <tr th:each="user : ${users}">
                <td th:text="${user.id}"></td>
                <td th:text="${user.name}"></td>
                <td th:text="${user.email}"></td>
                <td th:text="${user.age}"></td>
                <td th:text="${user.dob}"></td>
            </tr>
        </tbody>
    </table>
</body>
</html>

# JAVA-Demo
package ATM;

import java.util.ArrayList;
import java.util.Random;
import java.util.Scanner;

public class ATMsystem {
    public static void main(String[] args) {
        //1.定义账户类
        //2.定义一个集合容器 负责储存全部的账户对象 进行相关的业务操作
        ArrayList<Account> accounts = new ArrayList<>();
        Scanner sc = new Scanner(System.in);
        //3.展示系统的首页

        while (true) {
            System.out.println("==============ATM系统==================");
            System.out.println("1.账户登录");
            System.out.println("2.账户开户");

            System.out.println("请您选择操作：");
            int comment = sc.nextInt();

            switch (comment){
                case 1:
                    //用户登录操作
                    login(accounts,sc);
                    break;
                case 2:
                    //用户账户开户
                    register(accounts,sc);
                    break;
                default:
                    System.out.println("对不起 您输入的命令不存在");
            }
        }
    }

    /**
     * 登录功能
     * @param accounts  全部账户的集合
     * @param sc 扫描器
     */
    private static void login(ArrayList<Account> accounts, Scanner sc) {
        System.out.println("==============系统登录操作=================");
        //1.判断账户集合中是否存在账户
        if(accounts.size()==0){
            System.out.println("对不起 当前系统中没有任何账户 请先开户在进行登录");
            return;  //结束方法的执行
        }

        //2.正式进入登录操作
        while (true) {
            System.out.println("请您输入卡号:");
            String cardId = sc.next();
            //判断卡号是否存在
            Account acc = getAccountByCardId(cardId,accounts);
            if(acc != null){
                while (true) {
                    //卡号存在
                    //让用户输入密码 认证密码
                    System.out.println("请您输入密码：");
                    String password = sc.next();
                    //判断当前账户的密码是否与用户输入的密码一致
                    if(acc.getPassword().equals(password)){
                        //登陆成功
                        System.out.println("恭喜您"+acc.getUsername()+"先生/女士进入系统 您的卡号是"+acc.getCardID());
                        //......查询 转账 取款......
                        //展示登录后的操作页
                        showUserCommand(sc,acc,accounts);
                        return;//干掉登录方法
                    }else {
                        System.out.println("对不起 您输入的密码错误...");
                    }
                }
            }else {
                System.out.println("对不起 系统中不存在该账的卡号");
            }
        }

    }

    /**
     * 展示登录后的操作页
     */
    private static void showUserCommand(Scanner sc,Account acc,ArrayList<Account> accounts) {
        while (true) {
            System.out.println("===============用户操作页===================");
            System.out.println("1.查询账户");
            System.out.println("2.存款");
            System.out.println("3.取款");
            System.out.println("4.转账");
            System.out.println("5.修改密码");
            System.out.println("6.退出");
            System.out.println("7.注销账户");
            System.out.println("请选择");
            int command = sc.nextInt();
            switch (command){
                case 1:
                    //查询账户
                    showAccount(acc);
                    break;
                case 2:
                    //存款
                    depositMoney(acc,sc);
                    break;
                case 3:
                    drawMoney(acc,sc);
                    //取款
                    break;
                case 4:
                    //转账
                    transferMoney(acc,sc,accounts);
                    break;
                case 5:
                    //修改密码
                    updatePassword(sc,acc);
                   return;
                case 6:
                    //退出
                    System.out.println("退出成功 欢迎下次光临");
                   return;//干掉当前方法的执行
                case 7:
                    //注销账户
                    if(deleteAccount(acc,sc,accounts)){
                        //销户成功了 回到首页
                        return; //让当前方法停止执行 跳出去
                } else{
                        //还是在操作页停留 没有销户成功
                      break;
                }
                default:
                    System.out.println("您输入的操作命令不正确...");
            }
        }

    }

    /**
     * 注销账户功能
     * @param acc
     * @param sc
     * @param accounts
     */
    private static boolean deleteAccount(Account acc, Scanner sc, ArrayList<Account> accounts) {
        System.out.println("===============用户注销账户功能==================");
        //从当前账户集合中，删除当前对象 注销就完成
        System.out.println("您真的要注销账户？y/n");
        String rs = sc.next();
        switch (rs){
            case "y":
                //真正的注销账户
                //从当前账户集合中，删除当前对象 注销就完成
                if(acc.getMoney()>0){
                    System.out.println("还有钱呢 别注销了老铁");
                }else {
                    accounts.remove(acc);
                    System.out.println("您的账户注销账户完成");
                    return true; //销户成功
                }
                break;
            default:
                System.out.println("好的 当前账户继续保留");
        }
        return false;
    }

    /**
     *
     * @param sc 扫描器
     * @param acc 当前登录成功的账户对象
     */
    private static void updatePassword(Scanner sc, Account acc) {
        System.out.println("==========用户密码修改===============");
        while (true) {
            System.out.println("请您输入当前密码");
            String password = sc.next();
            //1.判断密码是否正确
            if(acc.getPassword().equals(password)){
                while (true) {
                    //密码正确
                    //2.输入新密码
                    System.out.println("请您输入新密码：");
                    String newPassword = sc.next();

                    System.out.println("请您再次输入新密码：");
                    String oknewPassword = sc.next();

                    if(newPassword.equals(oknewPassword)){
                        //密码一样 可以修改
                        acc.setPassword(newPassword);
                        System.out.println("恭喜你 密码修改成功...");
                        return;
                    }else {
                        System.out.println("您输入的两次密码不同");
                    }
                }
            }else {
                System.out.println("您输入的密码不正确");
            }
        }
    }

    /**
     * 转账功能
     * @param acc  自己的账户对象
     * @param sc  扫描器
     * @param accounts  全部的账户的集合
     */
    private static void transferMoney(Account acc, Scanner sc, ArrayList<Account> accounts) {
        System.out.println("=============用户转账操作=================");
        //1.判断是否足够两个账户
        if(accounts.size()<2){
            System.out.println("当前系统中不足两个账户 不能转账 请去开户吧");
            return;  //结束当前方法
        }
        //2.判断自己的账户是否有钱
        if(acc.getMoney() == 0){
            System.out.println("对不起 您自己都没钱 就别转账了");
            return;//结束当前方法
        }
        while (true) {
            //3.开始转账
            System.out.println("请您输入对方账户的卡号：");
            String cardId = sc.next();

            //这个卡号不能是自己的卡号
            if(cardId.equals(acc.getCardID())){
                System.out.println("对不起 您不可以给自己转账");
                continue;//结束当次执行
            }

            //判断卡号是否存在 根据卡号查询对方账户
            Account account = getAccountByCardId(cardId,accounts);
            if(account == null){
                System.out.println("对不起 您输入对方的账号不存在...");
               }else {
                // 这个账户对象存在 就行认证他的姓氏
                String userName = account.getUsername(); // 不能把全部的名字展示出来
                String tip = "*"+userName.substring(1);
                System.out.println("请您输入["+tip+"]的姓氏");
                String preName = sc.next();

                //认证姓氏是否输入正确
                if(userName.startsWith(preName)){
                    while (true) {
                        //认证通过 可以转账
                        System.out.println("请您输入转账金额：");
                        double money = sc.nextDouble();
                        //判断余额是否足够
                        if (money > acc.getMoney()){
                            System.out.println("对不起 余额不足 您最多可以转账"+acc.getMoney());
                        }else {
                          //余额足够 可以转账了
                          acc.setMoney(acc.getMoney()- money);
                          account.setMoney(acc.getMoney()+money);
                            System.out.println("转账成功 您的账户余额是"+ acc.getMoney());
                            return; //结束转账方法
                        }
                    }
                }else {
                    System.out.println("对不起 您输入的信息有错误...");
                }
            }
         }
    }

    /**
     * 取款功能
     * @param acc  当前账户对象
     * @param sc   扫描器
     */
    private static void drawMoney(Account acc, Scanner sc) {
        System.out.println("============用户取款操作=================");
        //1.判断是否足够100元
       if(acc.getMoney() < 100){
           System.out.println("对不起 当前账户中不够100元 不能取款");
           return;
       }
        while (true) {
            //2.提示用户输入取款金额
            System.out.println("请输入取款金额：");
            double money = sc.nextDouble();

            //3.判断这个金额是否满足要求
            if(money > acc.getQuatoMoney()){
                System.out.println("对不起 您当前的取款金额超过取款现额 每次最多可以取款："+ acc.getQuatoMoney());
            }else {
                //没有超过当次现额
            //4.判断是否超过了账户的总余额
            if (money > acc.getMoney()){
                System.out.println("余额不足 您账户目前总余额是："+acc.getMoney());
            }else {
                //可以取款
                System.out.println("恭喜您 取款成功"+ money + "元 成功");
                //更新余额
                acc.setMoney(acc.getMoney() -  money);
                //取款结束了
                showAccount(acc);
                return;//干掉取款方法
            }
            }
        }
    }

    /**
     * 存钱
     * @param acc  当前账户对象
     * @param sc   扫描器
     */
    private static void depositMoney(Account acc, Scanner sc) {
        System.out.println("=============用户存钱================");
        System.out.println("请您输入存款金额");
        double money = sc.nextDouble();

        //更新账户余额 原来的前加上新存的钱
        acc.setMoney(acc.getMoney() + money);
        System.out.println("恭喜您 存款成功 当前账户信息如下：");
        showAccount(acc);
    }

    /**
     * 展示账户信息
     * @param acc
     */
    private static void showAccount(Account acc) {
        System.out.println("==============当前账户信息如下============");
        System.out.println("卡号："+acc.getCardID());
        System.out.println("户主："+acc.getUsername());
        System.out.println("余额："+acc.getMoney());
        System.out.println("现额："+acc.getQuatoMoney());
    }

    /**
     * 用户开户功能的实现
     * @param accounts  接收账户集合
     */
    private static void register(ArrayList<Account> accounts,Scanner sc) {
        System.out.println("=========系统开户操作===========");
        //1.创建一个账户对象
        Account account = new Account();

        //2.录入当前账户的信息 注入到账户对象中去
        System.out.println("请您输入账户用户名：");
        String username =  sc.next();
        account.setUsername(username);

        while (true) {
            System.out.println("请您输入账户密码：");
            String password =  sc.next();
            System.out.println("请您再次输入账户密码：");
            String okpassword =  sc.next();
            if(okpassword.equals(password)){
                //密码通过
                account.setPassword(okpassword);
                break;
            }else {
                System.out.println("对不起 两次密码不一致 请重新确认");
            }
        }

        System.out.println("请您输入账户当次现额：");
        double quatoMoney =  sc.nextDouble();
        account.setQuatoMoney(quatoMoney);

        //为账户随机一个八位与其他账号不重复的卡号
        String cardId = getRandomCardId(accounts);
        account.setCardID(cardId);
        //3.把账户集合添加到账户集合中去
        accounts.add(account);
        System.out.println("恭喜您"+username+"先生/女士 您开户成功 您的卡号是："+cardId+"请您妥善保管");
    }

    /**
     * 为账户随机一个八位与其他账号不重复的卡号
     * @return
     */
    private static String getRandomCardId(ArrayList<Account>accounts) {
        Random r = new Random();
        while (true) {
            String cardId = "";
            for(int i =0;i<8;i++){
             cardId += r.nextInt(10);
            }

            //判断是否重复
            //根据这个卡号查询账户对象
            Account acc = getAccountByCardId(cardId,accounts);
            if(acc==null){
                //说明没有重复 可以使用这个号注册新账号
                return cardId;
            }
        }
    }

    /**
     * 根据卡号查询出一个账户对象
     * @param cardId  卡号
     * @param accounts 全部账户的集合
     * @return  账户对象 | null
     */
    public static  Account getAccountByCardId(String cardId,ArrayList<Account>accounts){
       for (int i =0;i<accounts.size();i++){
           Account acc = accounts.get(i);
           if(acc.getCardID().equals(cardId)){
               return acc;
           }
       }
       return null;//查无此账号
    }
}


package ATM;

public class Account {
    //成员变量
    private String cardID;
    private String username;
    private String password;
    private double money;//账户余额
    private double quatoMoney;//每次取现额度

    public String getCardID() {
        return cardID;
    }

    public void setCardID(String cardID) {
        this.cardID = cardID;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public double getMoney() {
        return money;
    }

    public void setMoney(double money) {
        this.money = money;
    }

    public double getQuatoMoney() {
        return quatoMoney;
    }

    public void setQuatoMoney(double quatoMoney) {
        this.quatoMoney = quatoMoney;
    }
}
